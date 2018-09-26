---
author: rosanevallim
title: Executing multiple ML models in a chain
description: This tutorial shows how to sequentially execute multiple machine learning models with the highest GPU performance
ms.author: rovalli
ms.date: 08/22/2018
ms.topic: article
ms.prod: windows
ms.technology: desktop
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Executing multiple ML models in a chain

Windows ML supports high-performance load and execution of model chains by carefully optimizing its GPU path. 
Model chains are defined by two or more models that execute sequentially, where the outputs of one model become the inputs to the next model down the chain. 

In order to explain how to efficiently chain models with Windows ML, let's use a FNS-Candy Style Transfer ONNX model as a toy example. You can find this type of model in the FNS-Candy Style Tranfer sample folder in our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/FNSCandyStyleTransfer).

Let's say we want to execute a chain that is composed of two instances of the same FNS-Candy model, here called **mosaic.onnx**. The application code would pass an image to the first model in the chain, let it compute the outputs, and then pass that transformed image to another instance of FNS-Candy, producing a final image.  

The following steps illustrate how to accomplish that using Windows ML.

>[!Note]
>In a real word scenario you most likely would use two different models, but this should be enough to illustrate the concepts.

1. First, let's load the **mosaic.onnx** model so that we can use it.
  ```cpp
  std::wstring filePath = L"path\\to\\mosaic.onnx"; 
  LearningModel model = LearningModel::LoadFromFilePath(filePath);
  ```

2. Then, let's create two identical sessions on the device's default GPU using the same model as input parameter. 
  ```cpp
  LearningModelSession session1(model, LearningModelDevice(LearningModelDeviceKind::DirectX));
  LearningModelSession session2(model, LearningModelDevice(LearningModelDeviceKind::DirectX));
  ```

> [!NOTE]
>In order to reap the performance benefits of chaining, you need to create identical GPU sessions for all of your models. Not doing so would result in additional data movement out of the GPU and into the CPU, which would reduce performance.

3. The following lines of code will create bindings for each session:
  ```cpp
  LearningModelBinding binding1(session1);
  LearningModelBinding binding2(session2);
  ```

4. Next, we will bind an input for our first model. We will pass in an image that is located in the same path as our model. In this example the image is called "fish_720.png".
  ```cpp
  //get the input descriptor
  ILearningModelFeatureDescriptor input = model.InputFeatures().GetAt(0);
  //load a SoftwareBitmap
  hstring imagePath = L"path\\to\\fish_720.png";

  // Get the image and bind it to the model's input
  try
  {
    StorageFile file = StorageFile::GetFileFromPathAsync(imagePath).get();
    IRandomAccessStream stream = file.OpenAsync(FileAccessMode::Read).get();
    BitmapDecoder decoder = BitmapDecoder::CreateAsync(stream).get();
    SoftwareBitmap softwareBitmap = decoder.GetSoftwareBitmapAsync().get();
    VideoFrame videoFrame = VideoFrame::CreateWithSoftwareBitmap(softwareBitmap);
    ImageFeatureValue image = ImageFeatureValue::CreateFromVideoFrame(videoFrame);
    binding1.Bind(input.Name(), image);
  }
  catch (...)
  {
    printf("Failed to load/bind image\n");
  }
  ```

5. In order for the next model in the chain to use the outputs of the evaluation of the first model, we need to create an empty output tensor and bind the output so we have a marker to chain with:
  ```cpp
  //get the output descriptor
  ILearningModelFeatureDescriptor output = model.OutputFeatures().GetAt(0);
  //create an empty output tensor 
  std::vector<int64_t> shape = {1, 3, 720, 720};
  TensorFloat outputValue = TensorFloat::Create(shape); 
  //bind the (empty) output
  binding1.Bind(output.Name(), outputValue);
  ```

> [!NOTE]
>You must use the **TensorFloat** data type when binding the output. This will prevent de-tensorization from occurring once evaluation for the first model is completed, therefore also avoiding additional GPU queueing for load and bind operations for the second model.

6. Now, we run the evaluation of the first model, and bind its outputs to the next model's input:
  ```cpp
  //run session1 evaluation
  VERIFY_NO_THROW(session1.EvaluateAsync(binding1, L""));
  //bind the output to the next model input
  binding2.Bind(input.Name(), outputValue);
  //run session2 evaluation
  auto session2AsyncOp = session2.EvaluateAsync(binding2, L"");
  ```

7. Finally, let's retrieve the final output produced after running both models by using the following line of code.
  ```cpp
  auto finalOutput = session2AsyncOp.get().Outputs().First().Current().Value();
  ```

That's it! Both your models now can execute sequentially by making the most of the available GPU resources. 





