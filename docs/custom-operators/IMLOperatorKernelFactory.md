---
author: eliotcowley
title: IMLOperatorKernelFactory interface
description: Implemented by the author of a custom operator kernel to create instances of that kernel.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorKernelFactory
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorKernelFactory
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorKernelFactory interface

Implemented by the author of a custom operator kernel to create instances of that kernel.

## Methods

| Name | Description |
|------|-------------|
| [CreateKernel](IMLOperatorKernelFactory_CreateKernel.md) | Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
