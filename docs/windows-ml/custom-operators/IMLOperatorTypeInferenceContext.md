---
title: IMLOperatorTypeInferenceContext interface
description: Provides information about an operator's usage while type inferrers are being invoked.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTypeInferenceContext
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTypeInferenceContext
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTypeInferenceContext interface

Provides information about an operator's usage while type inferrers are being invoked.

## Methods

| Name | Description |
|------|-------------|
| [GetInputCount](IMLOperatorTypeInferenceContext_GetInputCount.md) | Gets the number of inputs to the operator. |
| [GetInputEdgeDescription](IMLOperatorTypeInferenceContext_GetInputEdgeDescription.md) | Gets the description of the specified input edge of the operator. |
| [GetOutputCount](IMLOperatorTypeInferenceContext_GetOutputCount.md) | Gets the number of outputs to the operator. |
| [IsInputValid](IMLOperatorTypeInferenceContext_IsInputValid.md) | Returns true if an input to the operator is valid. |
| [IsOutputValid](IMLOperatorTypeInferenceContext_IsOutputValid.md) | Returns true if an output to the operator is valid. |
| [SetOutputEdgeDescription](IMLOperatorTypeInferenceContext_SetOutputEdgeDescription.md) | Sets the inferred type of an output edge. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
