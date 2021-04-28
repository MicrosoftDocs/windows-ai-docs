---
title: ILearningModelDeviceFactoryNative interface
description: Provides access to factory methods that enable the creation of ILearningModelDevice objects using ID3D12CommandQueue.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, ILearningModelDeviceFactoryNative
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- ILearningModelDeviceFactoryNative
api_location:
- windows.ai.machinelearning.native.h
---

# ILearningModelDeviceFactoryNative interface

Provides access to factory methods that enable the creation of [ILearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice) objects using [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue).

## Methods

| Name | Description |
|------|-------------|
| [CreateFromD3D12CommandQueue](ILearningModelDeviceFactoryNative_CreateFromD3D12CommandQueue.md) | Creates a [LearningModelDevice](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevice) that will run inference on the user-specified [ID3D12CommandQueue](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12commandqueue). |

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
