---
author: eliotcowley
title: IMLOperatorAttributes.GetAttribute method
description: Gets the value of an attribute element which is of a numeric type.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetAttribute
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]