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
| [GetExecutionInterface](IMLOperatorKernelCreationContext_GetExecutionInterface.md) | Returns an object whose supported interfaces vary based on the kernel type. |
| [GetInputCount](IMLOperatorKernelCreationContext_GetInputCount.md) | Gets the number of inputs to the operator. |
| [GetInputEdgeDescription](IMLOperatorKernelCreationContext_GetInputEdgeDescription.md) | Gets the description of the specified input edge of the operator. |
| [GetOutputCount](IMLOperatorKernelCreationContext_GetOutputCount.md) | Gets the number of outputs to the operator. |
| [GetOutputEdgeDescription](IMLOperatorKernelCreationContext_GetOutputEdgeDescription.md) | Gets the description of the specified output edge of the operator. |
| [GetTensorShapeDescription](IMLOperatorKernelCreationContext_GetTensorShapeDescription.md) | Gets the description of input and output shapes connected to operator edges. |
| [HasTensorShapeDescription](IMLOperatorKernelCreationContext_HasTensorShapeDescription.md) | Returns true if the description of input and output shapes connected to operator edges may be queried using **GetTensorShapeDescription**. |
| [IsInputValid](IMLOperatorKernelCreationContext_IsInputValid.md) | Returns true if an input to the operator is valid. This always returns true except for optional inputs. |
| [IsOutputValid](IMLOperatorKernelCreationContext_IsOutputValid.md) | Returns true if an output to the operator is valid. This always returns true except for optional outputs. |

[!INCLUDE [help](../includes/get-help.md)]