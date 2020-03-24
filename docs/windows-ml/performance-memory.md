---
author: walrusmcd
title: Windows ML performance and memory
description: Learn how to improve your application's performance when using Windows ML.
ms.author: paulm
ms.date: 7/2/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML performance and memory

In this article, we'll cover how to manage your application's performance when using Windows Machine Learning.

## Threading and concurrency

Every object exposed from the runtime is *agile*, meaning that they can be accessed from any thread. See [Agile objects in C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/agile-objects) for more on agile.

One key object you will work with is the [LearningModelSession](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession).  This object is always safe to call from any thread.

* **For GPU sessions**: The object will lock and synchronize concurrent calls.  If you require concurrency you need to create multiple sessions in order to achieve it.

* **For CPU sessions**: The object will not lock and will allow concurrent calls on a single session. You must take care to manage your own state, buffers, and binding objects.

You should take care and measure your goal for your scenario. Modern GPU architectures work differently than CPUs. For example, if low latency is your goal you might want to manage how you schedule work across your CPU and GPU engines using pipelining, not concurrency. [This article on multi-engine synchronization](https://docs.microsoft.com/windows/desktop/direct3d12/user-mode-heap-synchronization) is a great place to get started. If throughput is your goal (like processing as many images at a time as possible) you often want to use multiple threads and concurrency in order to saturate the CPU.

When it comes to threading and concurrency you want to run experiments and measure timings.   Your performance will change significantly based on your goals and scenario.

## Memory utilization

Each instance of [LearningModel](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel) and [LearningModelSession](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession) has a copy of the model in memory. If you're working with small models, you might not be concerned, but if you're working with very large models, this becomes important.

To release the memory, call **Dispose** on either the model or session. Don't just delete them, as some languages perform lazy garbage collection.

**LearningModel** keeps a copy in memory to enable new session creation. When you dispose the **LearningModel**, all existing sessions will continue to work.  However, you will no longer be able to create new sessions with that **LearningModel** instance. For large models, you can create a model and session, and then dispose the model. By using a single session for all calls to [Evaluate](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession.evaluate), you'll have a single copy of the large model in memory.

<!--
<TODO Asynchronous calling patterns>
-->

## Float16 support

For better performance and reduced model footprint, you can use [WinMLTools](convert-model-winmltools.md#convert-to-floating-point-16) to convert your model to float16.

Once converted, all weights and inputs are float16. Here's how you can work with float16 inputs and outputs:

* [ImageFeatureValue](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.imagefeaturevalue)
    * Recommended usage.
    * Converts colors and tensorizes into float16.
    * Supports bgr8 and 8-bit image formats, which can be converted safely into float16 without data loss.

* [TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat)
    * Advanced path.
    * Float32 cast into float16.
    * For images, this is safe to cast, as bgr8 is small and fits.
    * For non-images, [Bind](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelbinding.bind) will fail, and you will need to pass in a [TensorFloat16Bit](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat16bit) instead.

* [TensorFloat16Bit](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat16bit)
    * Advanced path.
    * You need to convert to float16 and pass the inputs as float32, which will be cast down into float16.

> [!NOTE]
> Most of the time, the operator is still performing 32-bit math. There is less risk for overflow, and the result is truncated to float16. However, if the hardware advertises float16 support, then the runtime will take advantage of it.

## Pre-processing input data

WinML performs some pre-processing steps under the covers to make processing input data simpler and more efficient. For example, given input images could be in various color formats and shapes, and may be different than what the model expects. WinML performs conversions on the images to match them up, reducing the load on the developer.

WinML also leverages the entire hardware stack (CPU, GPU, and so on) to provide the most efficient conversions for a particular device and scenario.

However, in certain cases you may want to manually tensorize your input data due to some specific requirements you have. For example, maybe you don't want to use [VideoFrame](https://docs.microsoft.com/uwp/api/windows.media.videoframe) for your images, or you want to normalize the pixel values from range 0-255 to range 0-1. In these cases, you can perform your own custom tensorization on the data. See the [Custom Tensorization Sample](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Samples/CustomTensorization) for an example of this.

[!INCLUDE [help](../includes/get-help.md)]
