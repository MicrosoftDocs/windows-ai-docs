---
title: ITensorNative.GetD3D12Resource method
description: Learn about the ITensorNative.GetD3D12Resource method. This method gets the tensor buffer as an ID3D12Resource.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, GetD3D12Resource
topic_type:
- APIRef
api_type:
- NA
api_name:
- ITensorNative.GetD3D12Resource
api_location:
- windows.ai.machinelearning.native.h
---

# ITensorNative.GetD3D12Resource method

Gets the tensor buffer as an [ID3D12Resource](/windows/desktop/api/d3d12/nn-d3d12-id3d12resource).

```cpp
HRESULT GetD3D12Resource(
    [out] ID3D12Resource ** result);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| result | [ID3D12Resource](/windows/desktop/api/d3d12/nn-d3d12-id3d12resource)** | The tensor buffer as an **ID3D12Resource**. |

## Returns

**HRESULT**
The result of the operation.

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
