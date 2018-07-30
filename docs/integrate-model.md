---
author: walrusmcd
title: Core concepts
description: Learn how to use the Windows ML APIs in your Windows applications.
ms.author: paulm
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Integrate a model into your app with Windows ML

The basic building blocks for Windows ML are:

* Models
* Devices
* Sessions	
* Bindings

You use these to Load, Bind, and Evaluate machine learning models. 

![load bind eval](images/load-bind-evaluate.png)


## Loading models
Windows ML uses ONNX as its format for model files.   ONNX is a industry standard interchange format you can use that works with most all the popular machine learning training frameworks.   You can use the converters to convert any models you already have (learn more here) or you can download existing ONNX files from popular catalogs like the ONNX model zoo (link) and the azure model zoo (link).

You generally distribute the model with you application.  You can include it in your APPX package or for desktop apps they can be anywhere you app has access to on the hard drive.    

There are several ways to load the models using static methods on LearningModel:
* LearningModel::LoadFromStreamAsync
* LearningModel::LoadFromStream
* LearningModel::LoadFromStorageFileAsync
* LearningModel::LoadFromFilePath

The stream versions of load() alllow applications to have more control over where the model comes from.  For example an app could choose to have the model encrypted on disk and decrypt it only in memory prior to calling load().    Other options include loading the model stream from a network share or other media.     Note:  Loading a model can take some time so take care to not call this from your UI thread.

RS5 Windows ML works with ONNX files that are in the 1.2.2 format.   This is opset 7.    The model formats uses in RS4 are 1.0 and will not load in RS5.    Click here to learn more about how to use the new converters run on your models.

## Reflecting on model schema

(TODO) 

## Choosing a Device

Device selection is done up front and then bound to a session.   For the lifetime of that session the same device will be used.   If that device becomes unavailable or if you choose to later use a different device, you must close and recreate a new session.

There are multiple ways you can choose which device to use.  The first is to use LearningModelDeviceKind.
    
* **Default**
	* Let the system decide which device to use.  In RS5 the system will choose "CPU" by default.  In later versions of windows this can change to include more advanced device selection logic.   We recommend using **Default** to get the flexibility of letting the system choose for you in the future.      
* **Cpu**
	* This forces to always use the CPU.  Even if there are other devices available. 
* **DirectX**     
	* This forces to use a DirectX hardware acceleration device.   It will use the first adapter enumerated by IDXGIFactory1::EnumAdapters().   
* **DirectXHighPerformance**
	* Same as DirectX but will use DXGI_GPU_PREFERENCE_HIGH_PERFORMANCE when enumerating adapters.   
* **DirectXMinPower**
	* Same as DirectX but will use DXGI_GPU_PREFERENCE_MINIMUM_POWER when enumerating adapters. 

### Device removal (advanced)

(TODO)

## Creating a Session
(TODO) 

## Binding inputs and outputs
Models specify their input and output features using a unique string name.   Before evaluating the model you can bind your inputs and output to the session using those names.   To do this you use the LearningModelBinding object which you can create based on a session.

### Tensors
Most of the types you interact with in a model are Tensors.   These are multi-dimensional arrays.  The most common tensor is a tensor of 32bit floats.  

The layout of tensors are row-major, with tightly packed contiguous data representing each dimension.  The total size being the multiplication product of the size of each dimension.

The majority of models you will find that Windows ML supports are going to be working with images.   Image classifiers as in our Squeezenet sample.

### Images
Images are represented in the model in a tensor format.    

#### Images as tensors
There are 2 things to consider when working with images as tensors:

1. Image formats

	Models are trained using a large set of image training data.   The weights are then saved and tailored for that training set.    The image you pass in when running evaluate() must match the same formats of the images that were used in training.   There are a variety of ways you can find out the correct format you should use.  Often you can ask the data scientist that trained the model, or in many cases the model is self-describing in what image formats it expects.

	New in ONNX is the support for meta data that allows the model to describe it image formats:  https://github.com/onnx/onnx/blob/master/docs/MetadataProps.md

	Most models use these formats, but this is not universal to all models.
		
	Image.BitmapPixelFormat	Bgr8
	Image.ColorSpaceGamma	SRGB
	Image.NominalPixelRange	NominalRange_0_255

2. Tensorization

	Converting from the image into the tensor is known as tensorization.   You general tensorize into and out of the model during evaluation.     Windows ML supports images using 4 dimensional tensors of 32bit floats in the "NCHW tensor format".   N is batch size (or number of images), C is channel count (1 for Gray8, 3 for Bgr8), H is height, and W is width.     In RS5 Windows ML supports a batch size N of 1.
	
	Each pixel of the image is an 8bit color number that is stored in the range of 0-255 and packed into a 32bit float.

#### How to pass images into the model
There are 2 ways you can pass images into models : 

1. ImageFeatureValue

	This is the recommended way of passing images as inputs and outputs.   It allows you to focus on the image and not have to worry about either conversions or tensorization.   You can create an ImageFeatureValue using the static method:  **ImageFeatureValue::CreateFromVideoFrame**
			
	We support 2 types of VideoFrames:   SoftwareBitmap and IDirect3DSurface.
	
	We will take care of both conversion and tensorization for the images to match the format the model requires.   The currently supported model format types are Gray8, Rgb8, and Bgr8, and the currently supported pixel range is 0-255.
	
	In order to find out what format the model needs, we use the following logic and precedence order:
	
	1. **BindWithProperties** will override all image settings.
	2. **Model metadata** will then be checked and used if available.
	3. **Best match** If no model metadata is provided, and no caller supplied properties, the runtime will attempt to make a best match.   If the tensor looks like NCHW (4 dim float32, N==1), the runtime will assume either Gray8 or Bgr8 depending on the channel count.

2. TensorFloat

	You can choose to do all of the conversions and tensorization yourself.   You would use this for cases that the model uses a color format or pixel range the runtime does not support.    
	
	To do this you do all the work to create a NCHW four dimensional tensor for 32bit floats and pass that in.
	
	When this code path is used, any image metadata on the model is ignored.
	
	We have a sample of how to do this here:
	
	<link to manual image tensorization sample>

### Maps

(TODO) 

### Sequences

(TODO) 

### Scalars
Most maps and sequences will have values that are scalars.  These show up where TensorFeatureDescriptor.Shape.Size is zero (0).   In this case the map or sequence will be of the scalar type.    The most common is <float>.   For example a string to float map would be:
	* MapFeatureDescriptor.KeyKind == TensorKind.String
	* MapFeatureDescriptor.ValueDescriptor.Kind == LearningModelFeatureKind.Tensor
	* MapFeatureDescriptor.ValueDescriptor.as<TensorFeatureDescriptor>().Shape.Size == 0
	* And the actual map feature value will be a IMap<string, float>
	

## Calling Evaluate

(TODO) 

## Performance features
### Memory utilization
Models can be large and you want to be aware of memory usage.   Examples can be anything from MNIST (2M), squeezenet (5MB), to VGG (500MB).   

Windows ML has a copy of the model in memory for each instance of LearningModel and LearningModelSession.

Your application can control memory usage by controlling the lifetime of these objects.   Both of these objects implement the IClosable interface so you can manage releasing these potentially large blocks of memory.   Take care not to just delete them as some languages perform lazy garbage collection.   By calling ::Close() explicitly you can control when the memory is released .   For small models you might not be concerned, but if you are working with very large models this becomes important.

One note on LearningModel is that it keeps a copy in memory to enable new session creation.   It is safe to dispose the LearningModel, all existing sessions will continue to work.  However you will no longer be able to create new sessions with that LearningModel instance.     For large models, you can choose to have a code path that creates the model, creates a single session for use, and then disposes the model.   By using that single session for all of your calls to Evaluate() you will make sure you only have a single copy of the large model in memory.

### Asynchronous calling patterns

(TODO) 

### Float16 support 

The first step in float16 support is running the ONNX tools to convert your model to float16 (link here).

Once you have a float16 model, here is how float16 works with Windows ML:

* All of the weights and inputs are float16 
* Note: Most of the time the operator is still performing 32bit math as that is what the hardware is best at (CPU&GPU).   Thus there is less risk for overflow, and the result is cast down (truncated) to float16.  However new hardware is on the horizon that can perform math at float16.    The runtime will assume float16 models have been certified to work with float16 math and if the hardware advertises float16 support, the runtime will take advantage of it. 
* Working with inputs and outputs
	* ImageFeatureValue
		* This is the recommended usage.
		* We will convert colors, and tensorize, into float16
		* This is safe to do as image formats supported are bgr8, 8bit and can always safely be tensorized into float16 without dataloss.  
	* TensorFloat
		* This is an advanced path.
		* Give us floats32, we allow floats32 and cast into float16
		* For images, this safe to cast as bgr8 is small and fits.
		* For non-images, we will fail the Bind call and you will need to take into account for quantization/cast and pass in a TensorFloat16 instead.
	* TensorFloat16bit
		* This is an advanced path.
		* This object allows you to do the work to convert to float16 using your app defined logic, and pass them into us as float32 where we will cast them down.



## Troubleshooting and debugging

### My model isn't producing the correct predictions

(TODO)

### My model fails to load

(TODO)

### My model runs too slowly

(TODO)

### My model uses too much memory

(TODO)

###EnableDebugOutput

(TODO)

###PIX

(TODO)

###ETL

(TODO)

## Visual Studio ML Code Generation

### Add a model with mlgen

Windows ML's code generator `mlgen` creates an interface (C# or C++/WinRT) with wrapper classes that call the [Windows ML API](/uwp/api/windows.ai.machinelearning.preview) for you, allowing you to easily load, bind, and evaluate the model in your project.

### mlgen

For UWP developers, `mlgen` is natively integrated with [Visual Studio (version 15.7)](https://developer.microsoft.com/windows/downloads). Inside your Visual Studio project, simply add your ONNX file to your project’s Assets, and VS will generate Windows ML wrapper classes in a new interface file.

For other workflows, or older versions of VS, you can use the command line tool `mlgen.exe`, which comes with the Windows SDK, to generate Windows ML wrapper classes. The tool is located in `(SDK_root)\bin\<version>\x64` or `(SDK_root)\bin\<version>\x86`, where SDK_root is the SDK installation directory. To run the tool, use the command below.

```
mlgen -i INPUT-FILE -l LANGUAGE -n NAMESPACE [-o OUTPUT-FILE]
```

Input parameters definition:

- `INPUT-FILE`: the ONNX model file
- `LANGUAGE`: CPP or CS
- `NAMESPACE`: the namespace of the generated code
- `OUTPUT-FILE`: file path where the generated code will be written to. If OUTPUT-FILE is not specified, the generated code is written to the standard output

The tool will output a file containing the interface classes, which you can then add to your VS project.

**Note**: To make sure your model builds when you compile your application, right click on the `.onnx` file, and select **Properties**. For **Build Action**, select **Content**.

Using the interface's generated wrapper classes, you'll follow the load, bind, and evaluate pattern to integrate your ML model into your app.

In the rest of the article, we'll use the mlgen interface from the [Get Started (UWP)](get-started-uwp.md) MNST model to demonstrate how to load, bind, and evaluate a model in your app.

### Load

Once you have the generated wrapper classes, you'll load the model in your app.

The MNISTModel class represents the MNIST model, and to load the model, we call the CreateMNISTModel method, passing in the ONNX file as the parameter.

```csharp
// Load the model
StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/MNIST.onnx"));
MNISTModel model = MNISTModel.CreateMNISTModel(modelFile);
```

### Bind

The generated code also includes Input and Output wrapper classes. The Input class represents the model's expected inputs, and the Output class represents the model's expected outputs.

To initialize the model's input object, call the Input class constructor, passing in your application data, and make sure that your input data matches the input type that your model expects. The MNISTModelInput class expects a VideoFrame, so we use a helper method to get a VideoFrame for the input.

```csharp
//Bind the input data to the model
MNISTModelInputs ModelInput = new MNISTModelInputs();
ModelInput.Input3 = await helper.GetHandWrittenImage(inkGrid);
```

Output objects are initialized to *Null* values and will be updated with the model's results after the next step, Evaluate.

### Evaluate

Once your inputs are initialized, call the model's EvaluateAsync method to evaluate your model on the input data. EvaluateAsync binds your inputs and outputs to the model object and evaluates the model on the inputs.

```csharp
// Evaluate the model
MNISTModelOuput ModelOutput = model.EvaluateAsync(ModelInput);
```

After evaluation, your output contains the model's results, which you now can view and analyze. Since the MNIST model outputs a list of probabilities, we iterate through the list to find and display the digit with the highest probability.

```csharp
 //Iterate through output to determine highest probability digit
float maxProb = 0;
int maxIndex = 0;
for (int i = 0; i < 10; i++)
{
    if (ModelOutput.Plus214_Output_0[i] > maxProb)
    {
        maxIndex = i;
        maxProb = ModelOutput.Plus214_Output_0[i];
    }
}
numberLabel.Text = maxIndex.ToString();
```