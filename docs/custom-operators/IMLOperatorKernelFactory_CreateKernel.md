---
author: eliotcowley
title: IMLOperatorKernelFactory.CreateKernel method
description: Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, CreateKernel
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelFactory.CreateKernel
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelFactory.CreateKernel method

Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object.

```cpp
void CreateKernel(
    IMLOperatorKernelCreationContext* context,
    _COM_Outptr_ IMLOperatorKernel** kernel)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
