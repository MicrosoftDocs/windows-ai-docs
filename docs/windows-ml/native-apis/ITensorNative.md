---
title: ITensorNative interface
description: Provides access to an ITensor as an array of bytes or ID3D12Resource objects.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, ITensorNative
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- ITensorNative
api_location:
- windows.ai.machinelearning.native.h
---

# ITensorNative interface

Provides access to an [ITensor](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.itensor) as an array of bytes or [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource) objects.

## Methods

| Name | Description |
|------|-------------|
| [GetBuffer](ITensorNative_GetBuffer.md) | Gets the tensor’s buffer as an array of bytes. |
| [GetD3D12Resource](ITensorNative_GetD3D12Resource.md) | Gets the tensor buffer as an [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource). |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
