---
title: IMLOperatorTensor.GetData method
description: Returns a pointer to byte-addressable memory for the tensor.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetData
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensor.GetData
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensor.GetData method

Returns a pointer to byte-addressable memory for the tensor. This may be used when [IMLOperatorTensor::IsDataInterface](IMLOperatorTensor_IsDataInterface.md) returns false, because the kernel was registered using [MLOperatorExecutionType::Cpu](MLOperatorExecutionType.md). The data size is derived from the tensor's shape. It is fully packed in memory.

```cpp
void* GetData()
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
