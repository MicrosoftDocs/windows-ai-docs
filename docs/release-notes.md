---
author: rosanevallim
title: Release notes
description: The latest updates on the Windows AI platform.
ms.author: rovalli
ms.date: 08/24/2018
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

## Get help

### Technical questions

To ask or answer technical questions about Windows ML, please use the **windows-machine-learning** tag on [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-machine-learning).

### Bugs

To report a bug, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/issues).

### Feature requests

To request a feature, please head over to [Windows Developer Feedback](https://wpdev.uservoice.com/).