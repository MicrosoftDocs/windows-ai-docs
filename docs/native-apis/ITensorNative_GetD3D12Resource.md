---
author: eliotcowley
title: ITensorNative.GetD3D12Resource method
description: Gets the tensor buffer as an ID3D12Resource.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, GetD3D12Resource
ms.localizationpriority: medium
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

Gets the tensor buffer as an [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource).

```cpp
HRESULT GetD3D12Resource(
    [out] ID3D12Resource ** result);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| result | [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource)** | The tensor buffer as an **ID3D12Resource**. |

## Returns

**HRESULT**
The result of the operation.

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../includes/get-help.md)]
