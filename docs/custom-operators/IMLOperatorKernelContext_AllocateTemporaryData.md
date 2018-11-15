---
author: eliotcowley
title: IMLOperatorKernelContext.AllocateTemporaryData method
description: Allocates temporary data which will be usable as intermediate memory for the duration of a call to **IMLOperatorKernel::Compute**.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, AllocateTemporaryData
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelContext.AllocateTemporaryData
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelContext.AllocateTemporaryData method

Allocates temporary data which will be usable as intermediate memory for the duration of a call to [IMLOperatorKernel::Compute](IMLOperatorKernel_Compute.md). This may be used by kernels registered using [MLOperatorExecutionType::D3D12](MLOperatorExecutionType.md). The data object supports the [ID3D12Resource](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource) interface, and is a GPU buffer.

```cpp
void AllocateTemporaryData(
    size_t size, 
    _COM_Outptr_ IUnknown** data)
```

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
