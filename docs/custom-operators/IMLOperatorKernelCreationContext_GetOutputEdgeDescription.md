---
author: eliotcowley
title: IMLOperatorKernelCreationContext.GetOutputEdgeDescription method
description: Gets the description of the specified output edge of the operator.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetOutputEdgeDescription
ms.localizationpriority: medium
---

# IMLOperatorKernelCreationContext.GetOutputEdgeDescription method

Gets the description of the specified output edge of the operator.

```cpp
void GetOutputEdgeDescription(
    uint32_t outputIndex, 
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

[!INCLUDE [help](../includes/get-help.md)]