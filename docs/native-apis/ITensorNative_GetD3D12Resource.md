---
author: eliotcowley
title: ITensorNative.GetD3D12Resource method
description: Gets the tensor buffer as an ID3D12Resource.
ms.author: elcowle
ms.date: 10/05/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, GetD3D12Resource
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]