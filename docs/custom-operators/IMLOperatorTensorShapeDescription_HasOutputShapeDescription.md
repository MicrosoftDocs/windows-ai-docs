---
author: eliotcowley
title: IMLOperatorTensorShapeDescription.HasOutputShapeDescription method
description: Returns true if output shapes may be queried using **GetOutputTensorDimensionCount** and **GetOutputTensorShape**.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, HasOutputShapeDescription
ms.localizationpriority: medium
---

# IMLOperatorTensorShapeDescription.HasOutputShapeDescription method

Returns true if output shapes may be queried using [IMLOperatorTensorShapeDescription::GetOutputTensorDimensionCount](IMLOperatorTensorShapeDescription_GetOutputTensorDimensionCount.md) and [IMLOperatorTensorShapeDescription::GetOutputTensorShape](IMLOperatorTensorShapeDescription_GetOutputTensorShape.md). This is true if the kernel was registered with a shape inferrer.

```cpp
bool HasOutputShapeDescription()
```

[!INCLUDE [help](../includes/get-help.md)]