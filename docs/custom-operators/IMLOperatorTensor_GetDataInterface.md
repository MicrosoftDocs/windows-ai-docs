---
author: eliotcowley
title: IMLOperatorTensor.GetDataInterface method
description: Gets an interface pointer for the tensor.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetDataInterface
ms.localizationpriority: medium
---

# IMLOperatorTensor.GetDataInterface method

Gets an interface pointer for the tensor. This may be used when [IMLOperatorTensor::IsDataInterface](IMLOperatorTensor_IsDataInterface.md) returns true, because the kernel was registered using [MLOperatorExecutionType::D3D12](MLOperatorExecutionType.md). The *dataInterface* object supports the [ID3D12Resource interface](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource), and is a GPU buffer.

```cpp
void GetDataInterface(
    _COM_Outptr_result_maybenull_ IUnknown** dataInterface)
```

[!INCLUDE [help](../includes/get-help.md)]