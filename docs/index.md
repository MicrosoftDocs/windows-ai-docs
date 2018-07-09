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

With Windows ML, you can use trained machine learning models in your Windows apps.

![Windows ML graphic](images/winml-graphic.png)

## Overview

:::row:::
    :::column:::
    Windows ML is a set of [WinRT APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) that allow you to use trained machine learning models in your Windows applications (C# and C++). Windows ML evaluates trained [ONNX models](https://onnx.ai) locally on Windows devices, providing hardware-optimized performance by leveraging the device's CPU or GPU.
    :::column-end:::
    :::column:::
        ![windows ml layers](images/winml-layers.png)
    :::column-end:::
:::row-end:::

To use Windows ML, you'll get an ONNX model, add it to your app, and integrate it into your code with the Windows ML APIs or tools.

![windows ml developer flow](images/winml-flow.png)

To get started, we recommend our [Get Started](get-started.md) tutorials. Then, browse our [How-to guide](how-to.md) to learn how to use the Windows ML APIs and tools.

## Resources

Topic | Description
- | -
[Get started](get-started.md) | Create your first Windows ML app with this step-by-step tutorial.
[How-to guide](how-to.md) | Learn how to get ONNX models, integrate them into your app, and achieve more advanced scenarios.
[Tutorials](tutorials.md) | Walk through sample apps that use Windows ML.
[API reference](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning) | Reference documentation.
[Code samples on GitHub](https://github.com/Microsoft/Windows-Machine-Learning) | See sample apps that demonstrate how to use Windows ML.

## Get Help

### Bugs and Feature Requests

To report a bug or make feature requests, please file an issue on our [GitHub](https://github.com/Microsoft/Windows-Machine-Learning).

### Technical Questions

If anything comes up that the documentation doesn't answer, you can send your questions to askwindowsml@microsoft.com.