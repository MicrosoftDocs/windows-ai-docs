---
author: serenaz
title: Overview of Windows AI
description:
ms.author: sezhen
ms.date: 06/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, windows machine learning
ms.localizationpriority: medium
---

# Overview

## What is Windows AI?

Windows AI is the platform for AI capabilities on Windows. With Windows AI, developers can take advantage of hardware optimizations and interchangeable AI models to build intelligent applications on Windows devices.

## What is Windows ML?

As part of the Windows AI platform, Windows ML is a set of [WinRT APIs](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.preview) (C# and C++) that allow developers to use machine learning in Windows applications. Windows ML evaluates trained models locally, providing hardware-accelerated performance by leveraging the device's CPU or GPU, and computes evaluations for both classical ML algorithms and Deep Learning.

### Local evaluation

Windows ML provides local, on-device evaluation, offering a number of benefits:

:::row:::
    :::column:::
        ![quick](images/i_quick-start.png)

        **Low latency**

        When scenarios require real-time results
    :::column-end:::
    :::column:::
        ![lock](images/i_lock.png)

        **Data privacy**

        When your customers prefer that you don’t send their data off the device
    :::column-end:::
    :::column:::
        ![offline](images/i_offline.png)

        **Offline**

        When there is no connectivity
    :::column-end:::
:::row-end:::

### Hardware optimization

Windows ML accelerates the evaluation of Deep Learning models using the GPU on DirectX12 capable devices, and CPU optimizations additionally enable high-performance evaluation of both classical ML and Deep Learning algorithms.

For computer vision scenarios, Windows ML simplifies and optimizes the use of image, video, and camera data by handling frame pre-processing and providing camera pipeline setup for model input.

### Interchangeable AI models

![ONNX](images/onnx.png)

Windows ML evaluates AI models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai) format. ONNX is an open format for ML models, allowing you to interchange models between various ML frameworks and tools.

### Developer flow

Windows ML simplifies the process of using machine learning in your app:

![windows ML developer flow](images/winmlstory.png)

1. Get an ONNX model.
2. Add the ONNX model file(s) to your project.
3. Load, bind, and evaluate the model in your application code.
4. Run on any Windows 10 device!

To learn more about how to develop with Windows ML, please see our [How-to](how-to.md) section.

### Video summary

> [!VIDEO https://www.youtube.com/embed/8MCDSlm326U]

### Supported versions and hardware

- [Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk) - Build 17110 or higher.
- which versions/devices of windows?

## See also

- [Blog: AI Platform for Windows Developers](https://blogs.windows.com/buildingapps/2018/03/07/ai-platform-windows-developers)