---
author: eliotcowley
title: Bind a model
description: Learn how to bind a model's inputs and outputs to pass information into and out of the model.
ms.author: elcowle
ms.date: 2/15/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Bind a model

A machine learning model has input and output features, which pass information into and out of the model.

After you load your model as a [LearningModel](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel), you can use [LearningModel.InputFeatures](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel.inputfeatures) and [LearningModel.OutputFeatures](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel.outputfeatures) to get [ILearningModelFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.ilearningmodelfeaturedescriptor) objects. These list the model's expected input and output feature types.

You use a [LearningModelBinding](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelbinding) to bind values to a feature, referencing the **ILearningModelFeatureDescriptor** by its [Name](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.ilearningmodelfeaturedescriptor.name) property.

The following video gives a brief overview of binding features of machine learning models.

<br/>

> [!VIDEO https://www.youtube.com/embed/WD5bNZwz2A8]

## Types of features

Windows ML supports all ONNX feature types, which are enumerated in [LearningModelFeatureKind](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelfeaturekind). These are mapped to different feature descriptor classes:

* **Tensor**: [TensorFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfeaturedescriptor)
* **Sequence**: [SequenceFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.sequencefeaturedescriptor)
* **Map**: [MapFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.mapfeaturedescriptor)
* **Image**: [ImageFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturedescriptor)

### Tensors

Tensors are multi-dimensional arrays, and the most common tensor is a tensor of 32-bit floats. The dimensions of tensors are row-major, with tightly packed contiguous data representing each dimension. The tensor's total size is the product of the sizes of each dimension.

### Sequences

Sequences are vectors of values. A common use of sequence types is a vector of float probabilities, which some classification models return to indicate the accuracy rating for each prediction. 

### Maps

Maps are key/value pairs of information. Classification models commonly return a string/float map that describes the float probability for each labeled classification name. For example, the output of a model that tries to predict the breed of dog in a picture might be `["Boston terrier", 90.0], ["Golden retriever", 7.4], ["Poodle", 2.6]`.

#### Scalars

Most maps and sequences will have values that are scalars. These show up where **TensorFeatureDescriptor.Shape.Size** is zero (0). In this case, the map or sequence will be of the scalar type. The most common is `float`. For example, a string to float map would be:

```cs
MapFeatureDescriptor.KeyKind == TensorKind.String
MapFeatureDescriptor.ValueDescriptor.Kind == LearningModelFeatureKind.Tensor
MapFeatureDescriptor.ValueDescriptor.as<TensorFeatureDescriptor>().Shape.Size == 0
```

The actual map feature value will be an `IMap<string, float>`.

### Images

When working with images, you'll need to be aware of image formats and tensorization.

#### Image formats

Models are trained with image training data, and the weights are saved and tailored for that training set. When you pass an image input into the model, its format must match the training images' format.

In many cases, the model describes the expected image format; ONNX models can use [metadata](https://github.com/onnx/onnx/blob/master/docs/MetadataProps.md) to describe expected image formats.  

Most models use the following formats, but this is not universal to all models:

* **Image.BitmapPixelFormat**: Bgr8
* **Image.ColorSpaceGamma**: SRGB
* **Image.NominalPixelRange**: NominalRange_0_255

#### Tensorization

Images are represented in Windows ML in a tensor format. Tensorization is the process of converting an image into a tensor and happens during bind.

Windows ML converts images into 4-dimensional tensors of 32-bit floats in the "NCHW tensor format":

* **N**: Batch size (or number of images). Windows ML currently supports a batch size N of 1.
* **C**: Channel count (1 for Gray8, 3 for Bgr8).
* **H**: Height.
* **W**: Width.

Each pixel of the image is an 8-bit color number that is stored in the range of 0-255 and packed into a 32-bit float.

#### How to pass images into the model

There are two ways you can pass images into models:

* [ImageFeatureValue](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue)

    We recommend using **ImageFeatureValue** to bind images as inputs and outputs, as it takes care of both conversion and tensorization, so the images match the model's required image format. The currently supported model format types are **Gray8**, **Rgb8**, and **Bgr8**, and the currently supported pixel range is 0-255.

    You can create an **ImageFeatureValue** using the static method [ImageFeatureValue.CreateFromVideoFrame](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue.createfromvideoframe).

    To find out what format the model needs, WinML uses the following logic and precedence order:

	1. [Bind(String, Object, IPropertySet)](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelbinding.bind#Windows_AI_MachineLearning_LearningModelBinding_Bind_System_String_System_Object_Windows_Foundation_Collections_IPropertySet_) will override all image settings.
	2. Model metadata will then be checked and used if available.
	3. If no model metadata is provided, and no caller supplied properties, the runtime will attempt to make a best match. If the tensor looks like NCHW (4-dimensional float32, N==1), the runtime will assume either **Gray8** or **Bgr8** depending on the channel count.

	There are several optional properties that you can pass into [Bind(String, Object, IPropertySet)](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelbinding.bind#Windows_AI_MachineLearning_LearningModelBinding_Bind_System_String_System_Object_Windows_Foundation_Collections_IPropertySet_):

	* [BitmapBounds](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbounds): If specified, these are the cropping boundaries to apply prior to sending the image to the model.
	* [BitmapPixelFormat](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmappixelformat): If specified, this is the pixel format that will be used as the model pixel format during image conversion.

	For image shapes, the model can specify either a specific shape that it takes (for example, SqueezeNet takes 224,224), or the model can specify free dimensions for any shape image (many StyleTransfer-type models can take variable sized images). The caller can use **BitmapBounds** to choose which section of the image they would like to use. If not specified, the runtime will scale the image to the model size (respecting aspect ratio) and then center crop.  

* [TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat)

    If Windows ML does not support your model's color format or pixel range, then you can implement conversions and tensorization. You'll create an NCHW four-dimensional tensor for 32-bit floats for your input value. See the [Custom Tensorization Sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization) for an example of how to do this.

    When this method is used, any image metadata on the model is ignored.

## Example

The following example shows how to bind to a model's input. In this case, we create a binding from *session*, create an **ImageFeatureValue** from *inputFrame*, and bind the image to the model's input, *inputName*.

```cs
private void BindModel(
    LearningModelSession session, 
    VideoFrame inputFrame, 
    string inputName) 
{
    // Create a binding object from the session
    LearningModelBinding binding = new LearningModelBinding(session);

    // Create an image tensor from a video frame
    ImageFeatureValue image = 
        ImageFeatureValue.CreateFromVideoFrame(inputFrame);

    // Bind the image to the input
    binding.Bind(inputName, image);
}
```

## See also

* Previous: [Create a session](create-a-session.md)
* Next: [Evaluate the model inputs](evaluate-model-inputs.md)

[!INCLUDE [help](includes/get-help.md)]
