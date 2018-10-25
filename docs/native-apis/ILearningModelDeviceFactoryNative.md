---
author: eliotcowley
title: ILearningModelDeviceFactoryNative interface
description: Provides access to factory methods that enable the creation of ILearningModelDevice objects using ID3D12CommandQueue.
ms.author: elcowle
ms.date: 10/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, ILearningModelDeviceFactoryNative
ms.localizationpriority: medium
---

# ILearningModelDeviceFactoryNative interface

Provides access to factory methods that enable the creation of [ILearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice) objects using [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue).

## Methods

| Name | Description |
|------|-------------|
| [CreateFromD3D12CommandQueue](ILearningModelDeviceFactoryNative_CreateFromD3D12CommandQueue.md) | Creates a [LearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice) that will run inference on the user-specified [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue). |

[!INCLUDE [help](../includes/get-help.md)]