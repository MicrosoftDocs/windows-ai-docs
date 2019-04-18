---
author: eliotcowley
title: IMLOperatorTensor interface
description: Representation of a tensor used during computation of custom operator kernels.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTensor
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTensor
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTensor interface

Representation of a tensor used during computation of custom operator kernels.

## Methods

| Name | Description |
|------|-------------|
| [GetData](IMLOperatorTensor_GetData.md) | Returns a pointer to byte-addressable memory for the tensor. |
| [GetDataInterface](IMLOperatorTensor_GetDataInterface.md) | Gets an interface pointer for the tensor. |
| [GetDimensionCount](IMLOperatorTensor_GetDimensionCount.md) | Gets the number of dimensions in the tensor. |
| [GetShape](IMLOperatorTensor_GetShape.md) | Gets the size of dimensions in the tensor. |
| [GetTensorDataType](IMLOperatorTensor_GetTensorDataType.md) | Gets the data type of the tensor. |
| [IsCpuData](IMLOperatorTensor_IsCpuData.md) | Indicates whether the memory used by the tensor is CPU-addressable. |
| [IsDataInterface](IMLOperatorTensor_IsDataInterface.md) | Whether the contents of the tensor are represented by an interface type, or byte-addressable memory. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
