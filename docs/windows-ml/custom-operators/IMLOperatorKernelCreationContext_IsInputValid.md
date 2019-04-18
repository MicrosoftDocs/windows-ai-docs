---
author: eliotcowley
title: IMLOperatorKernelCreationContext.IsInputValid method
description: Returns true if an input to the operator is valid.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IsInputValid
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelCreationContext.IsInputValid
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelCreationContext.IsInputValid method

Returns true if an input to the operator is valid. This always returns true except for optional inputs.

```cpp
bool IsInputValid(uint32_t inputIndex)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
