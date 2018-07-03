---
author: serenaz
title: Windows ML
description: Windows ML allows you to use trained machine learning models in your Windows applications.
ms.author: sezhen
ms.date: 06/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Windows ML

## About

:::row:::
    :::column:::
        Windows ML is a set of [WinRT APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.preview) that allow you to use trained machine learning models in your Windows applications (C# and C++). Windows ML evaluates trained [ONNX models](https://onnx.ai) locally on Windows devices, providing hardware-accelerated performance by leveraging the device's CPU or GPU.
    :::column-end:::
    :::column:::
        ![windows ml layers](images/winml-layers.png)
    :::column-end:::
:::row-end:::

## Develop

![Windows ML developer workflow](images/winmlstory.png)

- Install the Windows 10 SDK
- Get started
    - [UWP](get-started.md)
    - Win32/Desktop
- [How-to guide](how-to.md)
    - [Get an ONNX model](get-onnx-model.md)
    - [Convert existing models to ONNX](conversion-samples.md)
    - [Add models with mlgen](mlgen.md)
    - [Use the Windows ML APIs](winml-api.md)
- Tutorials
- API reference
- [Code samples on GitHub](https://github.com/Microsoft/Windows-Machine-Learning)

## Get Help

### Bugs and Feature Requests

To report a bug or make feature requestions, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning).

### Questions

If anything comes up that the documentation doesn't answer, you can send your questions to askwindowsml@microsoft.com.