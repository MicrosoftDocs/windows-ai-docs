---
author: serenaz
title: Windows ML
description: With Windows ML, you can use trained machine learning models in your Windows applications.
ms.author: sezhen
ms.date: 07/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML

With Windows ML, you can integrate trained machine learning models in your Windows apps.

![Windows ML graphic](images/winml-graphic.png)

## Overview

:::row:::
    :::column:::
    Windows ML allows you to use trained machine learning models in your Windows apps (C# and C++). The Windows ML inference engine evaluates models locally on Windows devices, removing concerns of connectivity, bandwidth, and data privacy. Hardware optimizations for CPU and GPU additionally enable high performance for quick evaluation results.
    :::column-end:::
    :::column:::
        ![windows ml layers](images/winml-layers.png)
    :::column-end:::
:::row-end:::

## Develop

![windows ml developer flow](images/winml-flow.png)

### System requirements

To build applications that use Windows ML, you'll need the [Windows 10 SDK](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK) - Build 17728 or higher.

### ONNX models

Windows ML evaluates models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format ([version 1.2.2](https://github.com/onnx/onnx/tree/rel-1.2.2)). ONNX is an open format for ML models, allowing you to interchange models between various [ML frameworks and tools](http://onnx.ai/supported-tools).

To get an ONNX model to use with Windows ML, you can:

- Download a pre-trained ONNX model from the [ONNX Models repo](https://github.com/onnx/models) or [Azure AI Gallery](https://gallery.azure.ai/browse?winml=true).
- [Train your own model](train-model-custom-vision.md) with Azure Custom Vision or VS Tools for AI, and export to ONNX format.
- Convert models trained in other frameworks (Caffe2, Chainer, CNTK, PyTorch, and more) into ONNX format with [WinMLTools](convert-model-winmltools.md) converters or [ONNX tutorials](https://github.com/onnx/tutorials).

Once you have an ONNX model, you'll integrate the model into your app's code with [mlgen](mlgen.md) or the [Windows ML APIs](winml-api-guide.md). Then, you'll be able use machine learning in your Windows apps and devices!

To learn more about how to use Windows ML, take a look through at our documentation, or at our [sample apps](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5) on GitHub.

## Documentation

| Topic | Description |
| - | - |
| [Release notes](release-notes.md) | Learn about the latest Windows ML features and fixes. |
| [Get started](get-started-uwp.md) | Create your first Windows ML app with this step-by-step tutorial. |
| [Train a model](train-model-custom-vision.md) | Train a model for Windows ML using Custom Vision. |
| [Convert a model](convert-model-winmltools.md) | Use WinMLTools to convert existing models to ONNX format for Windows ML. |
| Integrate a model | Integrate a model into your app with Windows ML tools or APIs. |

Other resources:

- [API reference](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning)
- [Windows-Machine-Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5) on GitHub.

## Get Help

### Technical Questions

To ask or answer technical questions about Windows ML, please use [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-machine-learning).

### Bugs

To report a bug, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/issues).

### Feature Requests

To request a feature, please head over to [Windows Developer Feedback](https://wpdev.uservoice.com/).