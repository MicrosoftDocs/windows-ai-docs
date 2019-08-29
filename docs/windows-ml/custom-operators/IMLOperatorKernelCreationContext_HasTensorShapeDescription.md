---
title: IMLOperatorKernelCreationContext.HasTensorShapeDescription method
description: Returns true if the description of input and output shapes connected to operator edges may be queried using **GetTensorShapeDescription**.
ms.date: 4/1/2019
ms.topic: article
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
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
