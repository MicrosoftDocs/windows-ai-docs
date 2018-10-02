---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.IsOutputValid method
description: Returns true if an output to the operator is valid.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IsOutputValid
ms.localizationpriority: medium
---

# IMLOperatorShapeInferenceContext.IsOutputValid method

Returns true if an output to the operator is valid. This always returns true except for optional outputs and invalid indices.

```cpp
bool IsOutputValid(
    uint32_t outputIndex)
```

[!INCLUDE [help](../includes/get-help.md)]