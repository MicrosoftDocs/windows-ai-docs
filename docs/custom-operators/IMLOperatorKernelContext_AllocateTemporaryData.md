---
author: eliotcowley
title: IMLOperatorKernelContext.AllocateTemporaryData method
description: Allocates temporary data which will be usable as intermediate memory for the duration of a call to **IMLOperatorKernel::Compute**.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, AllocateTemporaryData
ms.localizationpriority: medium
---

# IMLOperatorKernelContext.AllocateTemporaryData method

Allocates temporary data which will be usable as intermediate memory for the duration of a call to **IMLOperatorKernel::Compute**. This may be used by kernels registered using **MLOperatorExecutionType::D3D12**. The data object supports the **ID3D12Resource** interface, and is a GPU buffer.

```cpp
void AllocateTemporaryData(
    size_t size, 
    _COM_Outptr_ IUnknown** data)
```

[!INCLUDE [help](../includes/get-help.md)]