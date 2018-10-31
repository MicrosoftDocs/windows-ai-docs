---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.GetInputTensorDimensionCount method
description: Gets the number of dimensions of a tensor input of the operator.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputTensorDimensionCount
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensorShapeDescription.GetInputTensorDimensionCount
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensorShapeDescription.GetInputTensorDimensionCount method

Gets the number of dimensions of a tensor input of the operator. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensorDimensionCount(
    uint32_t inputIndex, 
    _Out_ uint32_t* dimensionCount)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
