---
title: IMLOperatorTensorShapeDescription.GetInputTensorDimensionCount method
description: Learn about the IMLOperatorTensorShapeDescription.GetInputTensorDimensionCount method. It gets the number of dimensions of a tensor input of the operator.
ms.date: 4/1/2019
ms.topic: article
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

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
