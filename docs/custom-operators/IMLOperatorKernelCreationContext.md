---
author: eliotcowley
title: IMLOperatorKernelCreationContext interface
description: Provides information about an operator's usage while kernels are being created.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorKernelCreationContext
ms.localizationpriority: medium
---

# IMLOperatorKernelCreationContext interface

Provides information about an operator's usage while kernels are being created.

## Methods

| Name | Description |
|------|-------------|
| [GetInputCount](IMLOperatorKernelCreationContext_GetInputCount.md) | Gets the number of inputs to the operator. |
| [GetOutputCount](IMLOperatorKernelCreationContext_GetOutputCount.md) | Gets the number of outputs to the operator. |
| [IsInputValid](IMLOperatorKernelCreationContext_IsInputValid.md) | Returns true if an input to the operator is valid. This always returns true except for optional inputs. |
| [IsOutputValid](IMLOperatorKernelCreationContext_IsOutputValid.md) | Returns true if an output to the operator is valid. This always returns true except for optional outputs. |

[!INCLUDE [help](../includes/get-help.md)]