---
title: IMLOperatorAttributes.GetAttribute method
description: Learn about the IMLOperatorAttributes.GetAttribute method. This method gets the value of an attribute element which is of a numeric type.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetAttribute
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorAttributes.GetAttribute
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorAttributes.GetAttribute method

Gets the value of an attribute element which is of a numeric type. For attributes which are of array types, this method queries an individual element within the attribute at the specified index.

```cpp
void GetAttribute(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    uint32_t elementCount,
    size_t elementByteSize,
    _Out_writes_bytes_(elementCount * elementByteSize) void* value)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
