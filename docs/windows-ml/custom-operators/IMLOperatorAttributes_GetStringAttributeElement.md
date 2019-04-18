---
author: eliotcowley
title: IMLOperatorAttributes.GetStringAttributeElement method
description: Gets the value of an attribute element which is of a string type.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetStringAttributeElement
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorAttributes.GetStringAttributeElement
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorAttributes.GetStringAttributeElement method

Gets the value of an attribute element which is of a string type. For attributes which are string arrays, this method queries the value of an individual element within the attribute at the specified index. The string is in UTF-8 format. The size includes the null termination character.

```cpp
void GetStringAttributeElement(
    _In_z_ const char* name,
    uint32_t elementIndex,
    uint32_t attributeElementByteSize,
    _Out_writes_(uint32_t) char* attributeElement)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
