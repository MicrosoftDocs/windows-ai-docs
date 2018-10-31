---
author: eliotcowley
title: IMLOperatorKernelContext.GetInputTensor method
description: Gets the input tensor of the operator at the specified index.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputTensor
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelContext.GetInputTensor
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelContext.GetInputTensor method

Gets the input tensor of the operator at the specified index. This sets the tensor to **nullptr** for optional inputs which do not exist. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensor(
    uint32_t inputIndex, 
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
