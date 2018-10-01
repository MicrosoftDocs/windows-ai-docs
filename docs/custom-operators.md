---
author: eliotcowley
title: Custom operators
description: TODO
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators
ms.localizationpriority: medium
---

# Custom operators

The Windows Machine Learning custom operator Win32 APIs are located in **MLOperatorAuthor.h**.

## APIs

The following is a list of the custom operator APIs with their syntax and descriptions.

### Enumerations

| Name | Description |
|------|-------------|
| [MLOperatorAttributeType](custom-operators/MLOperatorAttributeType.md) | Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type. |
| [MLOperatorEdgeType](custom-operators/MLOperatorEdgeType.md) | Specifies the types of an input or output edge of an operator. |
| [MLOperatorExecutionType](custom-operators/MLOperatorExecutionType.md) | Specifies whether a kernel uses the CPU or GPU for computation. |
| [MLOperatorKernelOptions](custom-operators/MLOperatorKernelOptions.md) | Specifies options used when registering custom operator kernels. |
| [MLOperatorParameterOptions](custom-operators/MLOperatorParameterOptions.md) | Specifies option flags of input and output edges of operators. |
| [MLOperatorSchemaEdgeTypeFormat](custom-operators/MLOperatorSchemaEdgeTypeFormat.md) | Specifies the manner in which types of input and output edges are described. |
| [MLOperatorTensorDataType](custom-operators/MLOperatorTensorDataType.md) | Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type. |

### Functions

| Name | Description |
|------|-------------|
| [MLCreateOperatorRegistry](custom-operators/MLCreateOperatorRegistry.md) | Creates an instance of **IMLOperatorRegistry** which may be used to register a custom operator kernel and custom operator schema. |

### Interfaces

| Name | Description |
|------|-------------|
| [IMLOperatorAttributes](custom-operators/IMLOperatorAttributes.md) | Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers. |
| [IMLOperatorKernel](custom-operators/IMLOperatorKernel.md) | Implemented by custom operator kernels. |
| [IMLOperatorKernelContext](custom-operators/IMLOperatorKernelContext.md) | Provides information about an operator's usage while kernels are being computed. |
| [IMLOperatorKernelCreationContext](custom-operators/IMLOperatorKernelCreationContext.md) | Provides information about an operator's usage while kernels are being created. |
| [IMLOperatorKernelFactory](custom-operators/IMLOperatorKernelFactory.md) | Implemented by the author of a custom operator kernel to create instances of that kernel. |
| [IMLOperatorRegistry](custom-operators/IMLOperatorRegistry.md) | Represents an instance of a registry for the custom operator kernel and schema. |
| [IMLOperatorShapeInferenceContext](custom-operators/IMLOperatorShapeInferenceContext.md) | Provides information about an operator's usage while shape inferrers are being invoked. |
| [IMLOperatorShapeInferrer](custom-operators/IMLOperatorShapeInferrer.md) | Implemented by shape inferrers to infer shapes of an operator's output tensor edges. |
| [IMLOperatorTensor](custom-operators/IMLOperatorTensor.md) | Representation of a tensor used during computation of custom operator kernels. |
| [IMLOperatorTensorShapeDescription](custom-operators/IMLOperatorTensorShapeDescription.md) | Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag. |
| [IMLOperatorTypeInferenceContext](custom-operators/IMLOperatorTypeInferenceContext.md) | Provides information about an operator's usage while type inferrers are being invoked. |
| [IMLOperatorTypeInferrer](custom-operators/IMLOperatorTypeInferrer.md) | Implemented by type inferrers to infer the types of an operator's output edges. |

### Structures

| Name | Description |
|------|-------------|
| [MLOperatorAttribute](custom-operators/MLOperatorAttribute.md) | Specifies the name and properties of an attribute of a custom operator. |
| [MLOperatorAttributeNameValue](custom-operators/MLOperatorAttributeNameValue.md) | Specifies the name and value(s) of an attribute of a custom operator. |
| [MLOperatorEdgeDescription](custom-operators/MLOperatorEdgeDescription.md) | Specifies the properties of an input or output edge of an operator. |
| [MLOperatorEdgeTypeConstraint](custom-operators/MLOperatorEdgeTypeConstraint.md) | Specifies constraints upon the types of edges supported in custom operator kernels and schema. |
| [MLOperatorKernelDescription](custom-operators/MLOperatorKernelDescription.md) | Description of a custom operator kernel used to register that schema. |
| [MLOperatorSchemaDescription](custom-operators/MLOperatorSchemaDescription.md) | Description of a custom operator schema used to register that schema. |
| [MLOperatorSchemaEdgeDescription](custom-operators/MLOperatorSchemaEdgeDescription.md) | Specifies information about an input or output edge of an operator. |
| [MLOperatorSetId](custom-operators/MLOperatorSetId.md) | Specifies the identity of an operator set. |

[!INCLUDE [help](includes/get-help.md)]