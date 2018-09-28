---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.GetOutputTensorDimensionCount method
description: Gets the number of dimensions of a tensor output of the operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetOutputTensorDimensionCount
ms.localizationpriority: medium
---

# IMLOperatorTensorShapeDescription.GetOutputTensorDimensionCount method

Gets the number of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor.

```cpp
GetOutputTensorDimensionCount(
    uint32_t outputIndex, 
    _Out_ uint32_t* dimensionCount)
```

[!INCLUDE [help](../includes/get-help.md)]