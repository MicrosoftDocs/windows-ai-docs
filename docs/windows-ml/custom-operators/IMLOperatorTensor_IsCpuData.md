---
title: IMLOperatorTensor.IsCpuData method
description: Learn about the IMLOperatorTensor.IsCpuData method. This method indicates whether the memory used by the tensor is CPU-addressable.
ms.date: 4/1/2019
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

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
