---
author: eliotcowley
title: IMLOperatorTensor.GetDataInterface method
description: Gets an interface pointer for the tensor.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, GetDataInterface
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensor.GetDataInterface
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensor.GetDataInterface method

Gets an interface pointer for the tensor. This may be used when [IMLOperatorTensor::IsDataInterface](IMLOperatorTensor_IsDataInterface.md) returns true, because the kernel was registered using [MLOperatorExecutionType::D3D12](MLOperatorExecutionType.md). The *dataInterface* object supports the [ID3D12Resource interface](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource), and is a GPU buffer.

```cpp
void GetDataInterface(
    _COM_Outptr_result_maybenull_ IUnknown** dataInterface)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
