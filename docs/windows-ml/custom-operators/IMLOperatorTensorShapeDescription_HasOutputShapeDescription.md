---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.HasOutputShapeDescription method
description: Returns true if output shapes may be queried using **GetOutputTensorDimensionCount** and **GetOutputTensorShape**.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, HasOutputShapeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensorShapeDescription.HasOutputShapeDescription
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensorShapeDescription.HasOutputShapeDescription method

Returns true if output shapes may be queried using [IMLOperatorTensorShapeDescription::GetOutputTensorDimensionCount](IMLOperatorTensorShapeDescription_GetOutputTensorDimensionCount.md) and [IMLOperatorTensorShapeDescription::GetOutputTensorShape](IMLOperatorTensorShapeDescription_GetOutputTensorShape.md). This is true if the kernel was registered with a shape inferrer.

```cpp
bool HasOutputShapeDescription()
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
