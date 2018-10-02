---
author: eliotcowley
title: IMLOperatorShapeInferenceContext.IsInputValid method
description: Returns true if an input to the operator is valid.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IsInputValid
ms.localizationpriority: medium
---

# IMLOperatorShapeInferenceContext.IsInputValid method

Returns true if an input to the operator is valid. This always returns true except for optional inputs and invalid indices.

```cpp
bool IsInputValid(
    uint32_t inputIndex)
```

[!INCLUDE [help](../includes/get-help.md)]