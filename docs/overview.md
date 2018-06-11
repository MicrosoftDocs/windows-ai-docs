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
# Windows AI Overview

![Windows AI graphic](images/brain.png)

## What is Windows AI?

Windows AI is a platform for AI capabilities and optimizations for Windows apps devices. With Windows AI, developers can take advantage of abstractions for hardware optimizations and interchangable AI models to build intelligent applications on Windows devices.

## Windows ML

Introduced in the Windows 10 SDK (v 11710), Windows ML provides hardware-accelerated evaluation of trained machine learning models on Windows 10 devices.

### Local evaluation

What are some benefits of local, on-device evalution with Windows ML?

:::row:::
    :::column:::
        ![quick](/media/common/i_quick-start.svg)
        ### Low latency
        When scenarios require real-time results
    :::column-end:::
    :::column:::
        ![lock](/media/common/i_lock.svg)
        ### Data privacy
        When your customers prefer that you don’t send their data off the device
    :::column-end:::
    :::column:::
        ![offline](/media/common/i_offline.svg)
        ### Offline
        When there is no connectivity
    :::column-end:::
:::row-end:::

### Hardware optimization

GPU acceleration, CPU optimization, camera pipeline

### Interchangeable AI models

ONNX

## Supported versions and hardware

- [Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk) - Build 17110 or higher.
- which versions/devices of windows?

## Try it out!

Create your first Windows ML app with a step-by-step tutorial in [Get Started](get-started.md), or learn more about the developer workflow in [How to develop with Windows ML](how-to.md).