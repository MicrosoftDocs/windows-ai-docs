---
author: eliotcowley
title: IMLOperatorTypeInferenceContext.SetOutputEdgeDescription method
description: Sets the inferred type of an output edge.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, SetOutputEdgeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTypeInferenceContext.SetOutputEdgeDescription
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTypeInferenceContext.SetOutputEdgeDescription method

Sets the inferred type of an output edge.

```cpp
void SetOutputEdgeDescription(
    uint32_t outputIndex, 
    const MLOperatorEdgeDescription* edgeDescription)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
