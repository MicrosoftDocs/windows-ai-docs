---
author: eliotcowley
title: IMLOperatorTensor.IsDataInterface method
description: Whether the contents of the tensor are represented by an interface type, or byte-addressable memory.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
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
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
