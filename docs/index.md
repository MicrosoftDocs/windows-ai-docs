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
    Windows ML allows you to use trained machine learning models in your Windows apps (C# and C++). The Windows ML inference engine evaluates trained models locally on Windows devices, removing concerns of connectivity, bandwidth, and data privacy. Hardware optimizations for CPU and GPU additionally enable high performance for quick evaluation results.
    :::column-end:::
    :::column:::
        ![windows ml layers](images/winml-layers.png)
    :::column-end:::
:::row-end:::

## Develop

![windows ml developer flow](images/winml-flow.png)

To use machine learning in your Windows apps with Windows ML, you'll:

1. [Get a trained ONNX model](get-onnx-model.md).
1. Add the model to your app.
1. [Integrate the model](integrate-model.md) into your app's code.
1. Run on any Windows device!

To learn how to develop with Windows ML, take a look through our documentation.

> [!WARNING]
> Windows ML is a **pre-released** product which may be substantially modified before it’s commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.
>
> To try out the pre-released Windows ML, you'll need the [Windows 10 Insider Preview Build](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewiso) (Build 17728 or higher) and the [Windows 10 SDK](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK) (Build 17723 or higher).

## Documentation

| Topic | Description |
| - | - |
| [Get ONNX models](get-onnx-model.md) | Download or train ONNX models for Windows ML. |
| [Convert a model](convert-model-winmltools.md) | Use WinMLTools to convert trained models to ONNX format. |
| [Integrate a model](integrate-model.md) | Integrate a model into your app's code with Windows ML tools or APIs. |
| [Improve performance](performance.md) | Learn about features to improve performance. |
| [Release notes](release-notes.md) | Learn about the latest Windows ML features and fixes. |
| [API reference](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) | See detailed API reference. |

If you'd like to see end-to-end code samples, we also have sample apps in our [Windows-Machine-Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/RS5) repo on Github.

## Get Help

### Technical Questions

To ask or answer technical questions about Windows ML, please use [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-machine-learning).

### Bugs

To report a bug, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning/issues).

### Feature Requests

To request a feature, please head over to [Windows Developer Feedback](https://wpdev.uservoice.com/).
