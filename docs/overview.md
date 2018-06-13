---
author: serenaz
title: Windows AI overview
description: Learn about Windows AI.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, windows machine learning
ms.localizationpriority: medium
---
# Windows AI overview

## What is Windows AI?

Windows AI is a platform for AI capabilities and optimizations for Windows devices. With Windows AI, developers can take advantage of abstractions for hardware optimizations and interchangeable AI models to build intelligent applications on Windows devices.

## Windows ML

Introduced in the Windows 10 SDK (v 11710), Windows ML provides hardware-accelerated evaluation of trained machine learning models on Windows 10 devices.

### Local evaluation

Local, on-device evalution with Windows ML?

:::row:::
    :::column:::
        ![quick](/media/common/i_quick-start.svg)
        **Low latency**
        When scenarios require real-time results
    :::column-end:::
    :::column:::
        ![lock](/media/common/i_lock.svg)
        **Data privacy**
        When your customers prefer that you don’t send their data off the device
    :::column-end:::
    :::column:::
        ![offline](/media/common/i_offline.svg)
        **Offline**
        When there is no connectivity
    :::column-end:::
:::row-end:::

### Hardware optimization

On DirectX12 capable devices, Windows ML accelerates the evaluation of Deep Learning models using the GPU. CPU optimizations additionally enable high-performance evaluation of both classical ML and Deep Learning algorithms. For computer vision scenarios, Windows ML simplifies and optimizes the use of image, video, and camera data by handling frame pre-processing and providing camera pipeline setup for model input.

### Interchangeable AI models

Windows ML evaluates AI models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools.

## How to develop with Windows ML

![windows ML developer flow](images/winmlstory.png)

1. Add the ONNX model file(s) to Visual Studio, and use Windows ML's automatic code generation, or call the Windows ML APIs directly.
2. Load, bind, and evaluate the model in your application code.
3. Run on any Windows 10 device!

## Supported versions and hardware

- [Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk) - Build 17110 or higher.
- which versions/devices of windows?

## Try it out!

Create your first Windows ML app with a step-by-step tutorial in [Get started](get-started.md), or learn more about the developer workflow in [How to develop with Windows ML](how-to.md).
