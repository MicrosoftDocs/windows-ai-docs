---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.GetOutputTensorDimensionCount method
description: Gets the number of dimensions of a tensor output of the operator.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetOutputTensorDimensionCount
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensorShapeDescription.GetOutputTensorDimensionCount
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensorShapeDescription.GetOutputTensorDimensionCount method

Gets the number of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor.

```cpp
GetOutputTensorDimensionCount(
    uint32_t outputIndex, 
    _Out_ uint32_t* dimensionCount)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
