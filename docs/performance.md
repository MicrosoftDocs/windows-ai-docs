---
author: walrusmcd
title: Performance
description: 
ms.author: paulm
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Improve Windows ML performance

> [!WARNING]
> Windows ML is a **pre-released** product which may be substantially modified before it’s commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.
>
> To try out the pre-released Windows ML, you'll need the [Windows 10 Insider Preview Build](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewiso) (Build 17728 or higher) and the [Windows 10 SDK](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK) (Build 17723 or higher).

In this article, we'll cover how to improve your app's performance when using Windows ML.

## Memory utilization

Models can be large and you want to be aware of memory usage.   Examples can be anything from MNIST (2M), squeezenet (5MB), to VGG (500MB).   

Windows ML has a copy of the model in memory for each instance of LearningModel and LearningModelSession.

Your application can control memory usage by controlling the lifetime of these objects.   Both of these objects implement the IClosable interface so you can manage releasing these potentially large blocks of memory.   Take care not to just delete them as some languages perform lazy garbage collection.   By calling ::Close() explicitly you can control when the memory is released .   For small models you might not be concerned, but if you are working with very large models this becomes important.

One note on LearningModel is that it keeps a copy in memory to enable new session creation.   It is safe to dispose the LearningModel, all existing sessions will continue to work.  However you will no longer be able to create new sessions with that LearningModel instance.     For large models, you can choose to have a code path that creates the model, creates a single session for use, and then disposes the model.   By using that single session for all of your calls to Evaluate() you will make sure you only have a single copy of the large model in memory.

<TODO Asynchronous calling patterns>

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