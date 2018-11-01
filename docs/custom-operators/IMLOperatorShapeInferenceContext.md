---
author: eliotcowley
title: IMLOperatorShapeInferenceContext interface
description: Provides information about an operator's usage while shape inferrers are being invoked.
ms.author: elcowle
ms.date: 11/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorShapeInferenceContext
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorShapeInferenceContext
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorShapeInferenceContext interface

Provides information about an operator's usage while shape inferrers are being invoked.

## Methods

| Name | Description |
|------|-------------|
| [GetInputCount](IMLOperatorShapeInferenceContext_GetInputCount.md) | Gets the number of inputs to the operator. |
| [GetInputEdgeDescription](IMLOperatorShapeInferenceContext_GetInputEdgeDescription.md) | Gets the description of the specified input edge of the operator. |
| [GetInputTensorDimensionCount](IMLOperatorShapeInferenceContext_GetInputTensorDimensionCount.md) | Gets the number of dimensions of a tensor output of the operator. |
| [GetInputTensorShape](IMLOperatorShapeInferenceContext_GetInputTensorShape.md) | Gets the sizes of dimensions of an input tensor of the operator. |
| [GetOutputCount](IMLOperatorShapeInferenceContext_GetOutputCount.md) | Gets the number of outputs to the operator. |
| [IsInputValid](IMLOperatorShapeInferenceContext_IsInputValid.md) | Returns true if an input to the operator is valid. |
| [IsOutputValid](IMLOperatorShapeInferenceContext_IsOutputValid.md) | Returns true if an output to the operator is valid. |
| [SetOutputTensorShape](IMLOperatorShapeInferenceContext_SetOutputTensorShape.md) | Sets the inferred shape of an output tensor. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
