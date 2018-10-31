---
author: eliotcowley
title: IMLOperatorKernelCreationContext.HasTensorShapeDescription method
description: Returns true if the description of input and output shapes connected to operator edges may be queried using **GetTensorShapeDescription**.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, HasTensorShapeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelCreationContext.HasTensorShapeDescription
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelCreationContext.HasTensorShapeDescription method

Returns true if the description of input and output shapes connected to operator edges may be queried using [IMLOperatorKernelCreationContext::GetTensorShapeDescription](IMLOperatorKernelCreationContext_GetTensorShapeDescription.md). This returns true unless the operator was registered using the [MLOperatorKernelOptions::AllowDynamicInputShapes](MLOperatorKernelOptions.md) flag.

```cpp
bool HasTensorShapeDescription()
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
