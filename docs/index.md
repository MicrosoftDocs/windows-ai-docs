---
author: serenaz
title: Windows ML
description: Windows ML allows you to use trained machine learning models in your Windows applications.
ms.author: sezhen
ms.date: 07/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML

With Windows ML, you can use trained machine learning models in your Windows apps.

![Windows ML graphic](images/winml-graphic.png)

## Overview

:::row:::
    :::column:::
    Windows ML is a set of [WinRT APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) that allow you to use trained machine learning models in your Windows applications (C# and C++). Windows ML evaluates trained [ONNX models](https://onnx.ai) locally on Windows devices, providing hardware-optimized performance by leveraging the device's CPU or GPU.

    Windows ML evaluates models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools. Windows ML supports the [1.2.2 release](https://github.com/onnx/onnx/tree/rel-1.2.2) of ONNX.

    :::column-end:::
    :::column:::
        ![windows ml layers](images/winml-layers.png)
    :::column-end:::
:::row-end:::

![windows ml developer flow](images/winml-flow.png)

To use Windows ML, you'll get an ONNX model, add it to your app, and integrate it into your code with the Windows ML APIs or tools.

## Resources

| Topic | Description |
| - | - |
| [Get started](get-started-uwp.md) | Create your first Windows ML app with this step-by-step tutorial. |
| [Train a model](train-model-custom-vision.md) | Train a model for Windows ML using Custom Vision. |
| [Convert a model](convert-model-winmltools.md) | Convert existing models to ONNX format to use with Windows ML. |
| [Integrate a model](integrate-model.md) | Integrate a model into your app by following the load, bind, and evaluate pattern. |

Other resources:

- [Code samples on GitHub](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5)
- [API reference](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning)

## Get Help

### Technical Questions

To ask or answer technical questions about Windows ML, please use [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-machine-learning).

### Bugs

To report a bug, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning).

### Feature Requests

To request a feature, please head to [Windows Developer Feedback](https://wpdev.uservoice.com/).