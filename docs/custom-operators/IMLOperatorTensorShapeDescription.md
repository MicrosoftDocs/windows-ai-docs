---
author: eliotcowley
title: IMLOperatorTensorShapeDescription interface
description: Represents the set of input and output tensor shapes of an operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTensorShapeDescription
ms.localizationpriority: medium
---

# IMLOperatorTensorShapeDescription interface

Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag.

## Methods

| Name | Description |
|------|-------------|
| [GetInputTensorDimensionCount](IMLOperatorTensorShapeDescription_GetInputTensorDimensionCount.md) | Gets the number of dimensions of a tensor input of the operator. Returns an error if the input at the specified index is not a tensor. |
| [GetInputTensorShape](IMLOperatorTensorShapeDescription_GetInputTensorShape.md) | Gets the sizes of dimensions of an input tensor of the operator. Returns an error if the input at the specified index is not a tensor. |
| [GetOutputTensorDimensionCount](IMLOperatorTensorShapeDescription_GetOutputTensorDimensionCount.md) | Gets the number of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor. |
| [HasOutputShapeDescription](IMLOperatorTensorShapeDescription_HasOutputShapeDescription.md) | Returns true if output shapes may be queried using **GetOutputTensorDimensionCount** and **GetOutputTensorShape**. This is true if the kernel was registered with a shape inferrer. |

[!INCLUDE [help](../includes/get-help.md)]