---
author: eliotcowley
title: IMLOperatorTensor.IsDataInterface method
description: Whether the contents of the tensor are represented by an interface type, or byte-addressable memory.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IsDataInterface
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensor.IsDataInterface
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensor.IsDataInterface method

Whether the contents of the tensor are represented by an interface type, or byte-addressable memory. This returns true when kernels are registered using [MLOperatorExecutionType::D3D12](MLOperatorExecutionType.md).

```cpp
bool IsDataInterface()
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
