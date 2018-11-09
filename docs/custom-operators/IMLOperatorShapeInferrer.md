---
author: eliotcowley
title: IMLOperatorShapeInferrer interface
description: Implemented by shape inferrers to infer shapes of an operator's output tensor edges.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorShapeInferrer
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorShapeInferrer
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorShapeInferrer interface

Implemented by shape inferrers to infer shapes of an operator's output tensor edges. Shape inferrers may be provided when registering custom operator kernels to improve performance and to enable the kernel to query the shape of its output tensors when it is created and computed. Shape inferrers may also be provided when registering custom operator schema to improve model validation.

## Methods

| Name | Description |
|------|-------------|
| [InferOutputShapes](IMLOperatorShapeInferrer_InferOutputShapes.md) | Called to infer shapes of an operator's output edges. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
