---
author: walrusmcd
title: Integrate a model into your app
description: Learn how to use Windows ML to integrate trained machine learning models into your Windows applications.
ms.author: paulm
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Integrate a model into your app with Windows ML

In this guide, we'll cover how to use Windows ML to integrate a model into your Windows app. Alternatively, if you'd like to use Windows ML's automatic code generator, check out [mlgen](mlgen.md).

> **Important APIs**: [Windows.AI.MachineLearning](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning)

We'll go over the basic building blocks of Windows ML, which are:

* Models
* Sessions
* Devices
* Bindings

You'll use these to load, bind, and evaluate your models with Windows ML.

## Load models

> [!IMPORTANT]
> Windows ML requires ONNX version 1.2.2.

Once you [get a trained ONNX model](get-onnx-model.md), you'll distribute the .onnx model file(s) with your app. You can include the .onnx file(s) in your APPX package, or, for desktop apps, they can be anywhere your app can access on the hard drive.

There are several ways to load the models using static methods on [**LearningModel**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel):

* LearningModel::LoadFromStreamAsync
* LearningModel::LoadFromStream
* LearningModel::LoadFromStorageFileAsync
* LearningModel::LoadFromFilePath

The stream versions of load() allow applications to have more control over where the model comes from. For example, an app could choose to have the model encrypted on disk and decrypt it only in memory prior to calling load(). Other options include loading the model stream from a network share or other media.

> [!NOTE]
> Loading a model can take some time, so take care not to call this from your UI thread.

## Create a session

Once you load a [**LearningModel**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel), you can create a [**LearningModelSession**](https://docs.microsoft.com/en-us/uwp/api/windows.ai.machinelearning.learningmodelsession), which binds the model to a device that runs and evaluates the model.

## Choose a device

You can select a device when you create a session. If the device becomes unavailable, or if you'd like to use a different device, you must close the session and create a new session.

You choose a device of type [**LearningModelDeviceKind**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevicekind
).

* **Default**
	* Let the system decide which device to use. Currently, the default device is CPU.
* **Cpu**
	* Use the CPU, even if other devices are available.
* **DirectX**
	* Use a DirectX hardware acceleration device, specifically the first adapter enumerated by IDXGIFactory1::EnumAdapters().
* **DirectXHighPerformance**
	* Same as DirectX but will use DXGI_GPU_PREFERENCE_HIGH_PERFORMANCE when enumerating adapters.
* **DirectXMinPower**
	* Same as DirectX but will use DXGI_GPU_PREFERENCE_MINIMUM_POWER when enumerating adapters.

If you don't specify a device, the system uses **Default**. We recommend using **Default** to get the flexibility of allowing the system choose for you in the future.

### Device removal (advanced)

In some cases, graphics devices might need to be unloaded and reloaded, as explained in the [DirectX documentation](https://docs.microsoft.com/windows/uwp/gaming/handling-device-lost-scenarios).

When using Windows ML, you'll need to detect this case and close the session. To recover from a device removal or re-initialization, you'll create a new session, which triggers the device selection logic to run again.

The most common case where you will see this error is during LearningModelSession::Evaluate(). In the case of device removal or reset, [LearningModelEvaluationResult::ErrorStatus](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelevaluationresult.errorstatus#Windows_AI_MachineLearning_LearningModelEvaluationResult_ErrorStatus) will be DXGI_ERROR_DEVICE_REMOVED or DXGI_ERROR_DEVICE_RESET.

## Reflect on model features

A machine learning model has input and output features, which pass information into and out of the model.

After you load your model as a LearningModel, you can use [LearningModel::InputFeatures()](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel.inputfeatures) and [LearningModel::OutputFeatures()](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel.outputfeatures) to get a list of the model's feature descriptors.

Windows ML supports all ONNX feature types, which are enumerated in [**LearningModelFeatureKind**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelfeaturekind):

* Tensor
* Sequence
* Map
* Image

You can reference a feature descriptor ([TensorFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfeaturedescriptor), [SequenceFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.sequencefeaturedescriptor), [MapFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.mapfeaturedescriptor), [ImageFeatureDescriptor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturedescriptor)) by its Name property.

## Bind inputs and outputs

You use [**LearningModelBinding**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelbinding) to bind values to a feature, referencing the feature by its Name property.

### Tensors

Most of the types you interact with in a model are Tensors. These are multi-dimensional arrays.  The most common tensor is a tensor of 32bit floats.  

The layout of tensors are row-major, with tightly packed contiguous data representing each dimension.  The total size being the multiplication product of the size of each dimension.

The majority of models you will find that Windows ML supports are going to be working with images. Image classifiers as in our Squeezenet sample.

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

	There are several optional properties that you can pass into BindWithProperties:

	* BitmapBounds - if specified this is the cropping boundaries to apply prior to sending the image to the model
	* BitmapPixelFormat - if specified this is the pixel format that will be used as the MODEL pixel format during image conversion.

	For image shapes, the model can specify either a specific shape that it takes (e.g. SqueezeNet takes 224,224) or the model can specify free dimensions such that it takes any shape image (many StyleTransfer type models can take variable sized images).   The caller can use BitmapBounds to choose which section of the image they would like to use.   If not specified, the runtime will scale the image to the model size (respecting aspect ratio) and then center crop.  

2. TensorFloat

	You can choose to do all of the conversions and tensorization yourself.   You would use this for cases that the model uses a color format or pixel range the runtime does not support.
	
	To do this you do all the work to create a NCHW four dimensional tensor for 32bit floats and pass that in.
	
	When this code path is used, any image metadata on the model is ignored.
	
	We have a sample of how to do this here:
	
	<link to manual image tensorization sample>

### Maps

Maps are key/value pairs of information.   A common use of map types are in classificaiton models.   They can choose to return a string/float map that describes the float probability for each labeled classification name.

### Sequences

Sequences are vectors of values.    A common use of sequence types is a vector of float probabilities.   Some classification models return a sequence of floats, which represent the resulting probabilities.  

### Scalars

Most maps and sequences will have values that are scalars.  These show up where TensorFeatureDescriptor.Shape.Size is zero (0).   In this case the map or sequence will be of the scalar type.    The most common is <float>.   For example a string to float map would be:
	* MapFeatureDescriptor.KeyKind == TensorKind.String
	* MapFeatureDescriptor.ValueDescriptor.Kind == LearningModelFeatureKind.Tensor
	* MapFeatureDescriptor.ValueDescriptor.as<TensorFeatureDescriptor>().Shape.Size == 0
	* And the actual map feature value will be a IMap<string, float>	

## Call evaluate

To run the model you call LearningModelSession::Evaluate(). There are 2 ways to call it: with or without a binding object. You can also choose to call it asynchronous or synchronous. When the Evaluation is done you can use the LearningModelEvaluationResult to look at the output features.