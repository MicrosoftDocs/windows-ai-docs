---
author: eliotcowley
title: IMLOperatorTensor.GetShape method
description: Gets the size of dimensions in the tensor.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetShape
ms.localizationpriority: medium
---

# IMLOperatorTensor.GetShape method

Gets the size of dimensions in the tensor.

```cpp
void GetShape(
    uint32_t dimensionCount,
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

[!INCLUDE [help](../includes/get-help.md)]