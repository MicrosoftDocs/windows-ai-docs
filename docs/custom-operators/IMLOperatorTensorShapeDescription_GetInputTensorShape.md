---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.GetInputTensorShape method
description: Gets the sizes of dimensions of an input tensor of the operator.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputTensorShape
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensorShapeDescription.GetInputTensorShape
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensorShapeDescription.GetInputTensorShape method

Gets the sizes of dimensions of an input tensor of the operator. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensorShape(
    uint32_t inputIndex, 
    uint32_t dimensionCount, 
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
