---
author: eliotcowley
title: IMLOperatorKernelCreationContext.GetTensorShapeDescription method
description: Gets the description of input and output shapes connected to operator edges.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetTensorShapeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelCreationContext.GetTensorShapeDescription
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelCreationContext.GetTensorShapeDescription method

Gets the description of input and output shapes connected to operator edges.

```cpp
void GetTensorShapeDescription(
    _COM_Outptr_ IMLOperatorTensorShapeDescription** shapeDescription)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
