---
author: eliotcowley
title: IMLOperatorKernelCreationContext.GetExecutionInterface method
description: Returns an object whose supported interfaces vary based on the kernel type.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetExecutionInterface
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelCreationContext.GetExecutionInterface
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelCreationContext.GetExecutionInterface method

Returns an object whose supported interfaces vary based on the kernel type. For kernels registered with [MLOperatorExecutionType::Cpu](MLOperatorExecutionType.md), *executionObject* will be set to **nullptr**. For kernels registered with **MLOperatorExecutionType::D3D12**, *executionObject* will support the [ID3D12GraphicsCommandList](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12graphicscommandlist) interface.

```cpp
void GetExecutionInterface(
    _COM_Outptr_result_maybenull_ IUnknown** executionObject)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
