---
author: eliotcowley
title: ITensorStaticsNative.CreateFromD3D12Resource method
description: TODO
ms.author: elcowle
ms.date: 10/05/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, CreateFromD3D12Resource
ms.localizationpriority: medium
---

# ITensorStaticsNative.CreateFromD3D12Resource method

TODO

```cpp
HRESULT CreateFromD3D12Resource(
    ID3D12Resource *value, 
    [size_is(shapeCount)] __int64 *shape, 
    int shapeCount, 
    [out] IUnknown ** result);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| value | [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource)* | TODO |
| shape | **__int64**\* | TODO |
| shapeCount | **int** | TODO |
| result | [IUnknown](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)** | TODO |

## Returns

**HRESULT**
TODO

[!INCLUDE [help](../includes/get-help.md)]