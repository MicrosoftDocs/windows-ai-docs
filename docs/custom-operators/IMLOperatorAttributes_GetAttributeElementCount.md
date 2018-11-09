---
author: eliotcowley
title: IMLOperatorAttributes.GetAttributeElementCount method
description: Gets the count of elements in an attribute.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetAttributeElementCount
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorAttributes.GetAttributeElementCount
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorAttributes.GetAttributeElementCount method

Gets the count of elements in an attribute. This may be used to determine if an attribute exists, and to determine the count of elements within an attribute of an array type.

```cpp
void GetAttributeElementCount(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    _Out_ uint32_t* elementCount)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
