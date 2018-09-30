---
author: eliotcowley
title: IMLOperatorKernel.Compute method
description: Computes the outputs of the kernel.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, Compute
ms.localizationpriority: medium
---

# IMLOperatorKernel.Compute method

Computes the outputs of the kernel. The implementation of this method should be thread-safe. The same instance of the kernel may be computed simultaneously on different threads.

```cpp
void Compute(
    IMLOperatorKernelContext* context)
```

[!INCLUDE [help](../includes/get-help.md)]