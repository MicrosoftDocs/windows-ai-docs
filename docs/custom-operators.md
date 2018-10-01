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

### Interfaces

| Name | Description |
|------|-------------|
| [IMLOperatorAttributes](custom-operators/IMLOperatorAttributes.md) | Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers. |
| [IMLOperatorKernel](custom-operators/IMLOperatorKernel.md) | Implemented by custom operator kernels. |
| [IMLOperatorKernelContext](custom-operators/IMLOperatorKernelContext.md) | Provides information about an operator's usage while kernels are being computed. |
| [IMLOperatorKernelCreationContext](custom-operators/IMLOperatorKernelCreationContext.md) | Provides information about an operator's usage while kernels are being created. |
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

### IMLOperatorKernelFactory interface

Implemented by the author of a custom operator kernel to create instances of that kernel.

#### CreateKernel method

Creates an instance of the associated operator kernel, given information about the operator's usage within a model described in the provided context object.

```cpp
void CreateKernel(
    IMLOperatorKernelCreationContext* context,
    _COM_Outptr_ IMLOperatorKernel** kernel)
```

### IMLOperatorRegistry interface

Represents an instance of a registry for the custom operator kernel and schema. Custom operators may be used with Windows.AI.MachineLearning APIs by returning instances of **IMLOperatorRegistry** through **ILearningModelOperatorProviderNative**.

#### RegisterOperatorSetSchema method

Registers a set of custom operator schema comprising an operator set. Operator sets follow the ONNX versioning design. Callers should provide schema for all operators that have changed between the specified baseline version and the version specified within *operatorSetId*. This prevents older versions of kernels from being used in models which import the newer operator set version. A type inferrer must be provided if the **MLOperatorSchemaDescription** structure cannot express how output types are determined. A shape inferrer may optionally be provided to enable model validation.

```cpp
void RegisterOperatorSetSchema(
    const MLOperatorSetId* operatorSetId,
    int32_t baselineVersion,
    _In_reads_opt_(schemaCount) const MLOperatorSchemaDescription* const* schema,
    uint32_t schemaCount,
    _In_opt_ IMLOperatorTypeInferrer* typeInferrer,
    _In_opt_ IMLOperatorShapeInferrer* shapeInferrer)
```

#### RegisterOperatorKernel method

Registers a custom operator kernel. A shape inferrer may optionally be provided.  This may improve performance and enables the kernel to query the shape of its output tensors when it is created and computed.

```cpp
void RegisterOperatorKernel(
    const MLOperatorKernelDescription* operatorKernel,
    IMLOperatorKernelFactory* operatorKernelFactory,
    _In_opt_ IMLOperatorShapeInferrer* shapeInferrer)
```

### MLCreateOperatorRegistry function

Creates an instance of **IMLOperatorRegistry** which may be used to register a custom operator kernel and custom operator schema.

```cpp
HRESULT MLCreateOperatorRegistry(
    _COM_Outptr_ IMLOperatorRegistry** registry)
```

[!INCLUDE [help](includes/get-help.md)]