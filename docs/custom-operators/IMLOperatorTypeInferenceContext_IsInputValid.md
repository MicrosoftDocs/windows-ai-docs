---
author: eliotcowley
title: IMLOperatorTypeInferenceContext.IsInputValid method
description: Returns true if an input to the operator is valid.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IsInputValid
ms.localizationpriority: medium
---

# IMLOperatorTypeInferenceContext.IsInputValid method

Returns true if an input to the operator is valid. This always returns true except for optional inputs.

```cpp
bool IsInputValid(
    uint32_t inputIndex)
```

[!INCLUDE [help](../includes/get-help.md)]