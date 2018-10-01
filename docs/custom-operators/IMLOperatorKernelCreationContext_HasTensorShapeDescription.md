---
author: eliotcowley
title: IMLOperatorKernelCreationContext.HasTensorShapeDescription method
description: Returns true if the description of input and output shapes connected to operator edges may be queried using **GetTensorShapeDescription**.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, HasTensorShapeDescription
ms.localizationpriority: medium
---

# IMLOperatorKernelCreationContext.HasTensorShapeDescription method

Returns true if the description of input and output shapes connected to operator edges may be queried using [IMLOperatorKernelCreationContext::GetTensorShapeDescription](IMLOperatorKernelCreationContext_GetTensorShapeDescription.md). This returns true unless the operator was registered using the [MLOperatorKernelOptions::AllowDynamicInputShapes](MLOperatorKernelOptions.md) flag.

```cpp
bool HasTensorShapeDescription()
```

[!INCLUDE [help](../includes/get-help.md)]