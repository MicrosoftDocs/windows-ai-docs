---
author: eliotcowley
title: IMLOperatorTensor.IsCpuData method
description: Indicates whether the memory used by the tensor is CPU-addressable.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IsCpuData
ms.localizationpriority: medium
---

# IMLOperatorTensor.IsCpuData method

Indicates whether the memory used by the tensor is CPU-addressable. This is true when kernels are registered using **MLOperatorExecutionType::Cpu**.

```cpp
bool IsCpuData()
```

[!INCLUDE [help](../includes/get-help.md)]