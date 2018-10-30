---
author: eliotcowley
title: IMLOperatorKernelContext interface
description: Provides information about an operator's usage while kernels are being computed.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorKernelContext
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelContext
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelContext interface

Provides information about an operator's usage while kernels are being computed.

## Methods

| Name | Description |
|------|-------------|
| [AllocateTemporaryData](IMLOperatorKernelContext_AllocateTemporaryData.md) | Allocates temporary data which will be usable as intermediate memory for the duration of a call to [IMLOperatorKernel::Compute](IMLOperatorKernel_Compute.md). |
| [GetExecutionInterface](IMLOperatorKernelContext_GetExecutionInterface.md) | Returns an object whose supported interfaces vary based on the kernel type. |
| [GetInputTensor](IMLOperatorKernelContext_GetInputTensor.md) | Gets the input tensor of the operator at the specified index. |
| [GetOutputTensor(uint32_t, IMLOperatorTensor**)](IMLOperatorKernelContext_GetOutputTensor.md#GetOutputTensor1) | Gets the output tensor of the operator at the specified index. |
| [GetOutputTensor(uint32_t, uint32_t, const uint32_t*, IMLOperatorTensor**)](IMLOperatorKernelContext_GetOutputTensor.md#GetOutputTensor2) | Gets the output tensor of the operator at the specified index, while declaring its shape. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
