---
author: walrusmcd
title: Windows ML API Guide
description: Learn how to use the Windows ML APIs in your Windows applications.
ms.author: paulm
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML API Guide

In this guide, you'll learn how to use the basic building blocks of the Windows ML APIs:

- Models
- Devices
- Sessions
- Bindings

## Loading models

Windows ML requires model files in ONNX format (v 1.2). To learn how to get an ONNX model, see our section on [ONNX models](overview.md#onnx-models).

You generally distribute the model with your application. You can include it in your `.appx` package, or for desktop apps, they can be anywhere your app can access on the hard drive.

Several static methods on LearningModel can load models:

- LearningModel::LoadFromStreamAsync
- LearningModel::LoadFromStream
- LearningModel::LoadFromStorageFileAsync
- LearningModel::LoadFromFilePath

The stream versions of load() alllow applications to have more control over where the model comes from.  For example, an app could choose to have the model encrypted on disk and decrypt it only in memory prior to calling load(). Other options include loading the model stream from a network share or other media. 

Note: Loading a model can take some time, so take care to not call this from your UI thread.

## Reflecting on model schema

(TODO)

## Choosing a Device

(TODO)

## Creating a Session

(TODO)

## Binding inputs and outputs

Models specify their input and output features using a unique string name. Before evaluating the model you can bind your inputs and output to the session using those names. To do this you use the LearningModelBinding object which you can create based on a session.

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

Converting from the image into the tensor is known as tensorization.   You general tensorize into and out of the model during evaluation.     Windows ML supports images using 4 dimensional tensors of 32bit floats in the "NCHW tensor format".   N is batch size (or number of images), C is channel count (1 for Gray8, 3 for Bgr8), H is height, and W is width. Windows ML supports a batch size N of 1.

Each pixel of the image is an 8bit color number that is stored in the range of 0-255 and packed into a 32bit float.

### How to pass images into the model

There are 2 ways you can pass images into models:

1. ImageFeatureValue

This is the recommended way of passing images as inputs and outputs.   It allows you to focus on the image and not have to worry about either conversions or tensorization.   You can create an ImageFeatureValue using the static method: ImageFeatureValue::CreateFromVideoFrame

We support 2 types of VideoFrames: SoftwareBitmap and IDirect3DSurface.

We will take care of both conversion and tensorization for the images to match the format the model requires.   The currently supported model format types are Gray8, Rgb8, and Bgr8, and the currently supported pixel range is 0-255.

In order to find out what format the model needs, we use the following logic and precedence order:

	1. BindWithProperties will override all image settings.
	2. Model metadata will then be checked.
	3. If no model metadata, and no caller supplied properties, the runtime attempt to make a best match.   If the tensor looks like NCHW (4 dim float32, N==1), the runtime will assume either Gray8 or Bgr8 depending on the channel count.

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

Most maps and sequences will have values that are scalars.  These show up where TensorFeatureDescriptor.Shape.Size is zero (0).   In this case the map or sequence will be of the scalar type.    The most common is <float>.

For example a string to float map would be:

* MapFeatureDescriptor.KeyKind == TensorKind.String
* MapFeatureDescriptor.ValueDescriptor.Kind == LearningModelFeatureKind.Tensor
* MapFeatureDescriptor.ValueDescriptor.as<TensorFeatureDescriptor>().Shape.Size == 0
* And the actual map feature value will be a IMap<string, float>

## Calling Evaluate

(TODO)

## Performance features

## Memory utilization

Models can be large and you want to be aware of memory usage.   Examples can be anything from MNIST (2M), squeezenet (5MB), to VGG (500MB).

Windows ML has a copy of the model in memory for each instance of LearningModel and LearningModelSession.

Your application can control memory usage by controlling the lifetime of these objects.   Both of these objects implement the IClosable interface so you can manage releasing these potentially large blocks of memory.   Take care not to just delete them as some languages perform lazy garbage collection.   By calling ::Close() explicitly you can control when the memory is released .   For small models you might not be concerned, but if you are working with very large models this becomes important.

One note on LearningModel is that it keeps a copy in memory to enable new session creation.   It is safe to dispose the LearningModel, all existing sessions will continue to work.  However you will no longer be able to create new sessions with that LearningModel instance.     For large models, you can choose to have a code path that creates the model, creates a single session for use, and then disposes the model.   By using that single session for all of your calls to Evaluate() you will make sure you only have a single copy of the large model in memory.

## Asynchronous calling patterns

(TODO)

## Float16 support

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