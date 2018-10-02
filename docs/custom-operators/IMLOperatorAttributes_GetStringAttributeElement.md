---
author: eliotcowley
title: IMLOperatorAttributes.GetStringAttributeElement method
description: Gets the value of an attribute element which is of a string type.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetStringAttributeElement
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]