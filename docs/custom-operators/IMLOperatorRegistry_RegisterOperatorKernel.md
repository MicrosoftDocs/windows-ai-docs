---
author: eliotcowley
title: IMLOperatorRegistry.RegisterOperatorKernel method
description: Registers a custom operator kernel.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, RegisterOperatorKernel
ms.localizationpriority: medium
---

# IMLOperatorRegistry.RegisterOperatorKernel method

Registers a custom operator kernel. A shape inferrer may optionally be provided.  This may improve performance and enables the kernel to query the shape of its output tensors when it is created and computed.

```cpp
void RegisterOperatorKernel(
    const MLOperatorKernelDescription* operatorKernel,
    IMLOperatorKernelFactory* operatorKernelFactory,
    _In_opt_ IMLOperatorShapeInferrer* shapeInferrer)
```

[!INCLUDE [help](../includes/get-help.md)]