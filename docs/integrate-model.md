---
author: walrusmcd
title: Integrate a model into your app
description: Learn how to use the Windows ML APIs in your Windows applications.
ms.author: paulm
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Integrate a model with Windows ML

In this guide, we'll cover how to use the [Windows.AI.MachineLearning APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) to integrate a model into your app.

We'll go over the basic building blocks of Windows ML, which are:

* Models
* Devices
* Sessions
* Bindings

## Loading models

Windows ML uses ONNX as its format for model files.   ONNX is a industry standard interchange format you can use that works with most all the popular machine learning training frameworks.   You can use the converters to convert any models you already have (learn more [here](convert-model-winmltools.md)) or you can download existing ONNX files from popular catalogs like the [ONNX Model Zoo](https://github.com/onnx/models) and the Azure AI Gallery(https://gallery.azure.ai/browse?winml=true).

You generally distribute the model with you application.  You can include it in your APPX package or for desktop apps they can be anywhere your app has access to on the hard drive.    

There are several ways to load the models using static methods on LearningModel:
* LearningModel::LoadFromStreamAsync
* LearningModel::LoadFromStream
* LearningModel::LoadFromStorageFileAsync
* LearningModel::LoadFromFilePath

The stream versions of load() alllow applications to have more control over where the model comes from. For example, an app could choose to have the model encrypted on disk and decrypt it only in memory prior to calling load().    Other options include loading the model stream from a network share or other media. 

>[!NOTE]
>Loading a model can take some time so take care to not call this from your UI thread.

RS5 Windows ML works with ONNX files that are in the 1.2.2 format.   This is opset 7.    The model formats used in RS4 are 1.0 and will not load in RS5. Click [here](convert-model-winmltools.md) to learn more about how to use the new converters on your models.

## Reflecting on model features

A machine learning model has input and output features.   This is how you pass information in and out of the model.  You can load a LearningModel to discover what features it works with.   Using both LearningModel::InputFeatures() and LearningModel::OutputFeatures() you can get back ILearningModelFeatureDescriptor's.    These tell you what sort of types the model uses.

Windows ML uses the ONNX interchange format and works wieth all of the feature types ONNX supports.  Features are references by a string name ILearningModelFeatureDescriptor::Name.

You can bind values to them using that name with a LearningModelBinding.   This is what you pass to LearningModelSession::Evaluate().   We support the following types of features:

  * Tensor
  * Sequence
  * Map
  * Image 

## Choosing a device

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

Graphics devices can hit cases where they are unloaded and need to be reloaded.  Like in explained here in the [DirectX documentation](https://docs.microsoft.com/en-us/windows/uwp/gaming/handling-device-lost-scenarios).

When this occurs using Windows ML, you need to detect this case and then close the session.  To recover from a device removal or re-initialization you simply create a new session, which will trigger the device selection logic to run again.   

The most common case where you will see this error is during LearningModelSession::Evaluate().   In the case of device removal or reset, LearningModelEvaluationResult::ErrorResult will be either DXGI_ERROR_DEVICE_REMOVED or DXGI_ERROR_DEVICE_RESET.

## Creating a session

Once you load a LearningModel, you can create LearningModelSessions in order to run them.    Each session binds that model to a device that is used to run the model.    

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

## Calling Evaluate

To run the model you call LearningModelSession::Evaluate().    There are 2 ways to call it: with or without a binding object.    You can also choose to call it asynchronous or synchronous.   When the Evaluation is done you can use the LearningModelEvaluationResult to look at the output features.
