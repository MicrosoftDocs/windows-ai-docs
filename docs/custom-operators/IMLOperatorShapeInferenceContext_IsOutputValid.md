---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.IsOutputValid method
description: Returns true if an output to the operator is valid.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IsOutputValid
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorShapeInferenceContext.IsOutputValid
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorShapeInferenceContext.IsOutputValid method

Returns true if an output to the operator is valid. This always returns true except for optional outputs and invalid indices.

```cpp
bool IsOutputValid(
    uint32_t outputIndex)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
