---
title: IMLOperatorKernel interface
description: Implemented by custom operator kernels.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorKernel
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernel
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernel interface

Implemented by custom operator kernels. A factory which creates instances of this interface is supplied when registering custom operator kernels using [IMLOperatorRegistry::RegisterOperatorKernel](IMLOperatorRegistry_RegisterOperatorKernel.md).

## Methods

| Name | Description |
|------|-------------|
| [Compute](IMLOperatorKernel_Compute.md) | Computes the outputs of the kernel. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
