---
title: IMLOperatorAttributes.GetStringAttributeElementLength method
description: Learn about the MLOperatorAttributes.GetStringAttributeElementLength method. This method gets the length of an attribute element which is of a string type.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetStringAttributeElementLength
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorAttributes.GetStringAttributeElementLength
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorAttributes.GetStringAttributeElementLength method

Gets the length of an attribute element which is of a string type. For attributes which are string arrays, this method queries the size of an individual element within the attribute at the specified index. The string is in UTF-8 format.  The size includes the null termination character.

```cpp
void GetStringAttributeElementLength(
    _In_z_ const char* name,
    uint32_t elementIndex,
    _Out_ uint32_t* attributeElementByteSize)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
