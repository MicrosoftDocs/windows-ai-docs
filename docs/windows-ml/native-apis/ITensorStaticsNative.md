---
title: ITensorStaticsNative interface
description: Provides access to factory methods that enable the creation of ITensor objects using ID3D12Resource.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, ITensorStaticsNative
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- ITensorStaticsNative
api_location:
- windows.ai.machinelearning.native.h
---

# ITensorStaticsNative interface

Provides access to factory methods that enable the creation of [ITensor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.itensor) objects using [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource).

## Methods

| Name | Description |
|------|-------------|
| [CreateFromD3D12Resource](ITensorStaticsNative_CreateFromD3D12Resource.md) | Creates a tensor object ([TensorFloat](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorfloat), [TensorInt32Bit](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.tensorint32bit)) from a user-specified [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource). |

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
