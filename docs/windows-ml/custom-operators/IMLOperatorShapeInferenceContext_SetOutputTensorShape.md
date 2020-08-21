---
title: IMLOperatorShapeInferenceContext.SetOutputTensorShape method
description: Learn about the IMLOperatorShapeInferenceContext.SetOutputTensorShape method. This method sets the inferred shape of an output tensor.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, SetOutputTensorShape
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorShapeInferenceContext.SetOutputTensorShape
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorShapeInferenceContext.SetOutputTensorShape method

Sets the inferred shape of an output tensor. Returns an error if the output at the specified index is not a tensor.

```cpp
void SetOutputTensorShape(
    uint32_t outputIndex,
    uint32_t dimensionCount,
    const uint32_t* dimensions)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
