---
author: eliotcowley
title: IMLOperatorKernelContext.GetOutputTensor method
description: Gets the output tensor of the operator at the specified index.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, GetOutputTensor
ms.localizationpriority: medium
---

# IMLOperatorKernelContext.GetOutputTensor method

## Overloads

| | |
|-|-|
| [GetOutputTensor(uint32_t, IMLOperatorTensor**)](#GetOutputTensor1) | Gets the output tensor of the operator at the specified index. |
| [GetOutputTensor(uint32_t, uint32_t, const uint32_t*, IMLOperatorTensor**)](#GetOutputTensor2) | Gets the output tensor of the operator at the specified index, while declaring its shape. |

<a name="GetOutputTensor1"></a>
## GetOutputTensor(uint32_t, IMLOperatorTensor**)

Gets the output tensor of the operator at the specified index. This sets the tensor to **nullptr** for optional outputs which do not exist. If the operator kernel was registered without a shape inference method, then the overload of **GetOutputTensor** which consumes the tensor's shape must be called instead. Returns an error if the output at the specified index is not a tensor.

```cpp
void GetOutputTensor(
    uint32_t outputIndex, 
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

<a name="GetOutputTensor2"></a>
## GetOutputTensor(uint32_t, uint32_t, const uint32_t*, IMLOperatorTensor**)

Gets the output tensor of the operator at the specified index, while declaring its shape. This returns **nullptr** for optional outputs which do not exist. If the operator kernel was registered with a shape inference method, then the overload of **GetOutputTensor** which doesn't consume a shape may also be called. Returns an error if the output at the specified index is not a tensor.

```cpp
void GetOutputTensor(
    uint32_t outputIndex,
    uint32_t dimensionCount,
    _In_reads_(dimensionCount) const uint32_t* dimensionSizes,
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

[!INCLUDE [help](../includes/get-help.md)]