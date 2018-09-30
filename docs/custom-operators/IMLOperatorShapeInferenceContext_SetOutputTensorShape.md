---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.SetOutputTensorShape method
description: Sets the inferred shape of an output tensor.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, SetOutputTensorShape
ms.localizationpriority: medium
---

# IMLOperatorShapeInferenceContext.SetOutputTensorShape method

Sets the inferred shape of an output tensor. Returns an error if the output at the specified index is not a tensor.

```cpp
void SetOutputTensorShape(
    uint32_t outputIndex, 
    uint32_t dimensionCount, 
    const uint32_t* dimensions)
```

[!INCLUDE [help](../includes/get-help.md)]