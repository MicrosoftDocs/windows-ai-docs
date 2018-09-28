---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.GetOutputTensorShape method
description: Gets the sizes of dimensions of a tensor output of the operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetOutputTensorShape
ms.localizationpriority: medium
---

# IMLOperatorTensorShapeDescription.GetOutputTensorShape method

Gets the sizes of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor.

```cpp
GetOutputTensorShape(
    uint32_t outputIndex, 
    uint32_t dimensionCount, 
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

[!INCLUDE [help](../includes/get-help.md)]