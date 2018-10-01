---
author: eliotcowley
title: IMLOperatorTypeInferenceContext interface
description: Provides information about an operator's usage while type inferrers are being invoked.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTypeInferenceContext
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]