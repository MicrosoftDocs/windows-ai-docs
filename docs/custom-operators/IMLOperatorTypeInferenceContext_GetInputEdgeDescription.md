---
author: eliotcowley
title: IMLOperatorTypeInferenceContext.GetInputEdgeDescription method
description: Gets the description of the specified input edge of the operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetInputEdgeDescription
ms.localizationpriority: medium
---

# IMLOperatorTypeInferenceContext.GetInputEdgeDescription method

Gets the description of the specified input edge of the operator.

```cpp
void GetInputEdgeDescription(
    uint32_t inputIndex,
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

[!INCLUDE [help](../includes/get-help.md)]