---
author: rosanevallim
title: Release notes
description: Learn about the latest updates on the Windows AI platform. See known issues and view additional available resources.
ms.author: rovalli
ms.date: 04/08/2021
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Release notes

This page records updates to Windows ML in the latest builds of the Windows 10 SDK and NuGet Package.

## Windows ML NuGet Package - Version 1.7

- [Download NuGet here](https://www.nuget.org/packages/Microsoft.AI.MachineLearning)
- [Built on OINNX Runtime 1.7](https://github.com/microsoft/onnxruntime/releases)
- .NET5 support - will work with .NET5 Standard 2.0 Projections.
- Image descriptors expose NominalPixelRange properties
- Native support added for additional pixel ranges [0..1] and [-1..1] in image models.
- A new property is added to the ImageFeatureDescriptor runtimeclass to expose the ImageNominalPixelRange property in ImageFeatureDescriptor. Other similar properties exposed are the image’s BitmapPixelFormat and BitmapAlphaMode.
- Bug fixes and performance improvements.
- DirectML PIX markers to Redist added to enable profiling graph at operator level.
- Fixes applied to ensure the package correctly installs on C# UWP projects in Visual Studio.


## Windows ML NuGet Package - Version 1.6
- [Download NuGet here](https://www.nuget.org/packages/Microsoft.AI.MachineLearning)
- [Built on OINNX Runtime 1.6](https://github.com/microsoft/onnxruntime/releases)
- Support for UWP applications targeting Windows Store deployment for both CPU and GPU.
- WindowsAI Redist now includes a statically linked C-Runtime package for additional deployment options.
- Minor API Improvements: Users are now able to bind Iterable as inputs and outputs, and able to create Tensor* via multiple buffers.

## Windows ML NuGet Package - Version 1.5

- Support for UWP applications targeting Windows Store deployment (CPU only).
- Support for .NET and .NET framework applications.
- Support for RUST Developers - [sample and documentation available](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/RustSqueezenet)
- New APIs to for additional performance control:
   * [IntraopNumThreads](/native-apis/IntraopNumThreads.md): Provides an ability to change the number of threads used in the threadpool for Intra Operator Execution for CPU operators through LearningModelSessionOptions.
   * [SetNamedDimensionOverrides]((/native-apis/SetNamedDimensionOverrides.md): Provides the ability to override named input dimensions to concrete values through LearningModelSessionOptions in order to achieve better runtime performance.
- Support for additional ONNX format image type denotations – Gray8, normalized [0..1] and normalized [-1..1].
- Reduced package size by separating debug symbols into separate distribution package.


## Windows ML NuGet Package – Version 1.4

- [Download NuGet here](https://www.nuget.org/packages/Microsoft.AI.MachineLearning)
- [Built on ONNX Runtime 1.4](https://github.com/microsoft/onnxruntime/releases)
- Support for ONNX 1.6 and opset 11.
- General usability and performance improvements.



## Windows ML NuGet Package - Version 1.3

- [Download NuGet here](https://www.nuget.org/packages/Microsoft.AI.MachineLearning)
- [Built on ONNX Runtime 1.3](https://github.com/microsoft/onnxruntime/releases)
- Corresponds to MachineLearningContract v3.
- Support for ONNX 1.6 and opset 11.
- CPU execution supported down to Windows 8.1; GPU execution supported down to Windows 10 version 1709.
- Certified known tested paths are Desktop Applications using C++. Store applications and the Windows Application Certification Kit are not yet supported. 

## Build 19041 (Windows 10, version 2004)

Support for ONNX 1.4 and opset 9 (CPU and GPU) 

API Surface additions:
* [CloseModelOnSessionCreation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsessionoptions.closemodelonsessioncreation?view=winrt-19041): new **LearningModelSessionOptions** parameter to configure to reduce working memory.

Tooling:

* WinMLTools converters supports new ONNX versions and opset  
* Optimizations to WinMLRunner exposing new performance metrics 

## Build 18362 (Windows 10, version 1903)

All features and updates from previous flighted builds:

* ONNX 1.3 support
* Support for model size reduction via post-training weight quantization. You can use the latest version of WinMLTools to pack the weights of your model down to int8.
* Removal of mlgen from the Windows 10 SDK&mdash;use one of the following Visual Studio extensions instead:
    * Visual Studio 2017: [Windows Machine Learning Code Generator VS 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen)
    * Visual Studio 2019: [Windows Machine Learning Code Generator](https://marketplace.visualstudio.com/items?itemName=WinML.mlgenv2)

## Build 18829

* [mlgen](mlgen.md) has been removed from the Windows 10 SDK. Instead, install one of the following Visual Studio extensions depending on your version:
    * Visual Studio 2017: [Windows Machine Learning Code Generator VS 2017](https://marketplace.visualstudio.com/items?itemName=WinML.mlgen)
    * Visual Studio 2019: [Windows Machine Learning Code Generator](https://marketplace.visualstudio.com/items?itemName=WinML.mlgenv2)

## Build 18290
- Min supported ONNX version = 1.2.2 (opset 7)
- Max supported ONNX version = 1.3 (opset 8)
- Supports model size reduction via post-training weight quantization. You can use the latest version of WinMLTools to pack the weights of your model down to int8.

## Build 17763 (Windows 10, version 1809)

* First official release of Windows Machine Learning.
* Requires ONNX [v1.2](https://github.com/onnx/onnx/tree/rel-1.2.2).
* [Windows.AI.MachineLearning.Preview namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.preview) deprecated in favor of [Windows.AI.MachineLearning namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning).

### Known issues

* For models containing sequences, MLGen generates an **IList&lt;Dictionary&lt;*key*, *value*&gt;&gt;** instead of the proper **IList&lt;IDictionary&lt;*key*, *value*&gt;&gt;**, leading to empty results. To fix this issue, simply replace the automatically generated code with the appropriate **IList&lt;IDictionary&lt;*key*, *value*&gt;&gt;** instead.

## Build 17723

- Requires ONNX [v1.2](https://github.com/onnx/onnx/tree/rel-1.2.2).
- Supports F16 datatypes with GPU-based model inferences for [better performance](performance-memory.md) and reduced model footprint. You can use WinMLTools to [convert your models from FP32 to FP16](convert-model-winmltools.md#convert-to-floating-point-16).
- Allows desktop apps to consume [Windows.AI.MachineLearning APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) with [WinRT/C++](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/).

[!INCLUDE [help](../includes/get-help.md)]
