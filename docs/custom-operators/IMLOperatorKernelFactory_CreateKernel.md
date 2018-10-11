---
author: eliotcowley
title: IMLOperatorKernelFactory.CreateKernel method
description: Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, CreateKernel
ms.localizationpriority: medium
---

# IMLOperatorKernelFactory.CreateKernel method

Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object.

```cpp
void CreateKernel(
    IMLOperatorKernelCreationContext* context,
    _COM_Outptr_ IMLOperatorKernel** kernel)
```

[!INCLUDE [help](../includes/get-help.md)]