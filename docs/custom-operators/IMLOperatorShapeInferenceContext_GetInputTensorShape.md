---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.GetInputTensorShape method
description: Gets the sizes of dimensions of an input tensor of the operator.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputTensorShape
ms.localizationpriority: medium
---

# IMLOperatorShapeInferenceContext.GetInputTensorShape method

Gets the sizes of dimensions of an input tensor of the operator. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensorShape(
    uint32_t inputIndex,
    uint32_t dimensionCount,
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

[!INCLUDE [help](../includes/get-help.md)]