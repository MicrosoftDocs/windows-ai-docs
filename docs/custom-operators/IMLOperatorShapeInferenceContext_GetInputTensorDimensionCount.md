---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.GetInputTensorDimensionCount method
description: Gets the number of dimensions of a tensor output of the operator.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputTensorDimensionCount
ms.localizationpriority: medium
---

# IMLOperatorShapeInferenceContext.GetInputTensorDimensionCount method

Gets the number of dimensions of a tensor output of the operator.

```cpp
void GetInputTensorDimensionCount(
    uint32_t inputIndex,
    _Out_ uint32_t* dimensionCount)
```

[!INCLUDE [help](../includes/get-help.md)]