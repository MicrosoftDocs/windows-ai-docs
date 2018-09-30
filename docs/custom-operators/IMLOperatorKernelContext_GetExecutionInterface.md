---
author: eliotcowley
title: IMLOperatorKernelContext.GetExecutionInterface method
description: Returns an object whose supported interfaces vary based on the kernel type.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetExecutionInterface
ms.localizationpriority: medium
---

# IMLOperatorKernelContext.GetExecutionInterface method

Returns an object whose supported interfaces vary based on the kernel type. For kernels registered with **MLOperatorExecutionType::Cpu**, *executionObject* will be set to **nullptr**. For kernels registered with **MLOperatorExecutionType::D3D12**, *executionObject* will support the **ID3D12GraphicsCommandList** interface. This may be a different object than was provided to **IMLOperatorKernelCreationContext::GetExecutionInterface** when the kernel instance was created.

```cpp
void GetExecutionInterface(
    _COM_Outptr_result_maybenull_ IUnknown** executionObject)
```

[!INCLUDE [help](../includes/get-help.md)]