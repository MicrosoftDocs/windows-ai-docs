---
author: eliotcowley
title: IMLOperatorTensor.IsCpuData method
description: Indicates whether the memory used by the tensor is CPU-addressable.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IsCpuData
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensor.IsCpuData
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensor.IsCpuData method

Indicates whether the memory used by the tensor is CPU-addressable. This is true when kernels are registered using [MLOperatorExecutionType::Cpu](MLOperatorExecutionType.md).

```cpp
bool IsCpuData()
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
