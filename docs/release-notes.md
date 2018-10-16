---
author: rosanevallim
title: Release notes
description: The latest updates on the Windows AI platform.
ms.author: rovalli
ms.date: 09/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Release notes

This page records updates to Windows ML in the latest builds of the Windows 10 SDK.

## Build 17723

- Requires ONNX [v1.2](https://github.com/onnx/onnx/tree/rel-1.2.2) or higher.
- Supports F16 datatypes with GPU-based model inferences for [better performance](performance-memory.md) and reduced model footprint. You can use WinMLTools to [convert your models from FP32 to FP16](convert-model-winmltools.md#convert-to-floating-point-16).
- Allows desktop apps to consume [Windows.AI.MachineLearning APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) with [WinRT/C++](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/).

## Build 17763

* First official release of Windows Machine Learning.
* [Windows.AI.MachineLearning.Preview namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.preview) deprecated in favor of [Windows.AI.MachineLearning namespace](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning).

[!INCLUDE [help](includes/get-help.md)]