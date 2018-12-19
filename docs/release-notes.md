---
author: rosanevallim
title: Release notes
description: The latest updates on the Windows AI platform.
ms.author: rovalli
ms.date: 11/2/2018
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Release notes

This page records updates to Windows ML in the latest builds of the Windows 10 SDK.

## Build 18290
- Min supported ONNX version = 1.2.2 (opset 7)
- Max supported ONNX version = 1.3 (opset 8)
- Supports linearly quantized models. You can use the latest version of WinMLTools to [quantize your models](convert-model-winmltools.md#quantize-onnx-model).

## Build 17763

* First official release of Windows Machine Learning.
* Requires ONNX [v1.2](https://github.com/onnx/onnx/tree/rel-1.2.2).
* [Windows.AI.MachineLearning.Preview namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.preview) deprecated in favor of [Windows.AI.MachineLearning namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning).

### Known issues

* For models containing sequences, MLGen generates an **IList&lt;Dictionary&lt;*key*, *value*&gt;&gt;** instead of the proper **IList&lt;IDictionary&lt;*key*, *value*&gt;&gt;**, leading to empty results. To fix this issue, simply replace the automatically generated code with the appropriate **IList&lt;IDictionary&lt;*key*, *value*&gt;&gt;** instead.

## Build 17723

- Requires ONNX [v1.2](https://github.com/onnx/onnx/tree/rel-1.2.2).
- Supports F16 datatypes with GPU-based model inferences for [better performance](performance-memory.md) and reduced model footprint. You can use WinMLTools to [convert your models from FP32 to FP16](convert-model-winmltools.md#convert-to-floating-point-16).
- Allows desktop apps to consume [Windows.AI.MachineLearning APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) with [WinRT/C++](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/).

[!INCLUDE [help](includes/get-help.md)]
