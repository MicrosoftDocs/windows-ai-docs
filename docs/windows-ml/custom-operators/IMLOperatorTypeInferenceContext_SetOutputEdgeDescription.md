---
title: IMLOperatorTypeInferenceContext.SetOutputEdgeDescription method
description: Learn about the IMLOperatorTypeInferenceContext.SetOutputEdgeDescription method. This method sets the inferred type of an output edge.
ms.date: 4/1/2019
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

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
