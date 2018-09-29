---
author: eliotcowley
title: IMLOperatorTensor interface
description: Representation of a tensor used during computation of custom operator kernels.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTensor
ms.localizationpriority: medium
---

# IMLOperatorTensor interface

Representation of a tensor used during computation of custom operator kernels.

## Methods

| Name | Description |
|------|-------------|
| [GetData](IMLOperatorTensor_GetData.md) | Returns a pointer to byte-addressable memory for the tensor. |
| [GetDimensionCount](IMLOperatorTensor_GetDimensionCount.md) | Gets the number of dimensions in the tensor. |
| [GetShape](IMLOperatorTensor_GetShape.md) | Gets the size of dimensions in the tensor. |
| [GetTensorDataType](IMLOperatorTensor_GetTensorDataType.md) | Gets the data type of the tensor. |
| [IsCpuData](IMLOperatorTensor_IsCpuData.md) | Indicates whether the memory used by the tensor is CPU-addressable. |
| [IsDataInterface](IMLOperatorTensor_IsDataInterface.md) | Whether the contents of the tensor are represented by an interface type, or byte-addressable memory. |

[!INCLUDE [help](../includes/get-help.md)]