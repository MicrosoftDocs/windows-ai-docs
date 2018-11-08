---
author: eliotcowley
title: MLCreateOperatorRegistry function
description: Creates an instance of **IMLOperatorRegistry** which may be used to register a custom operator kernel and custom operator schema.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLCreateOperatorRegistry
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLCreateOperatorRegistry
api_location:
- MLOperatorAuthor.h
---

# MLCreateOperatorRegistry function

Creates an instance of [IMLOperatorRegistry](IMLOperatorRegistry.md) which may be used to register a custom operator kernel and custom operator schema.

```cpp
HRESULT MLCreateOperatorRegistry(
    _COM_Outptr_ IMLOperatorRegistry** registry)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
