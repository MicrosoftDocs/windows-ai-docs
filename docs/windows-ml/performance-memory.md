---
author: walrusmcd
title: Windows ML performance and memory
description: Learn how to improve your app's performance when using Windows ML.
ms.author: paulm
ms.date: 6/12/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML performance and memory

In this article, we'll cover how to improve your app's performance when using Windows ML.

## Memory utilization

Each instance of [**LearningModel**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel) and [**LearningModelSession**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession) has a copy of the model in memory. If you're working with small models, you might not be concerned, but if you're working with very large models, this becomes important.

To release the memory, call Dispose() on either the model or session. Don't just delete them, as some languages perform lazy garbage collection.

**LearningModel** keeps a copy in memory to enable new session creation. When you dispose the LearningModel, all existing sessions will continue to work.  However, you will no longer be able to create new sessions with that LearningModel instance. For large models, you can create a model and session, and then dispose the model. By using a single session for all calls to Evaluate(), you'll have a single copy of the large model in memory.

<TODO Asynchronous calling patterns>

## Float16 support

For better performance and reduced model footprint, you can use [WinMLTools](convert-model-winmltools.md#convert-to-floating-point-16) to convert your model to float16.

Once converted, all weights and inputs are float16. Here's how you can work with float16 inputs and outputs:

* [**ImageFeatureValue**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue)
    * Recommended usage.
    * Converts colors and tensorizes into float16.
    * Supports bgr8 and 8bit image formats, which can be converted safely into float16 without data loss.  
* [**TensorFloat**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat)
    * Advanced path.
    * Float32 cast into float16.
    * For images, this safe to cast, as bgr8 is small and fits.
    * For non-images, Bind() will fail, and you will need to pass in a TensorFloat16Bit instead.
* [**TensorFloat16Bit**](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat16bit)
    * Advanced path.
    * You need to convert to float16 and pass the inputs as float32, which will be cast down into float16.

**Note**: Most of the time, the operator is still performing 32bit math. There is less risk for overflow, and the result is truncated to float16. However, if the hardware advertises float16 support, then the runtime will take advantage of it.

[!INCLUDE [help](../includes/get-help.md)]
