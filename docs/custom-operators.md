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
| [IMLOperatorTensor](custom-operators/IMLOperatorTensor.md) | Representation of a tensor used during computation of custom operator kernels. |
| [IMLOperatorTensorShapeDescription](custom-operators/IMLOperatorTensorShapeDescription.md) | Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag. |
| [IMLOperatorTypeInferenceContext](custom-operators/IMLOperatorTypeInferenceContext.md) | Provides information about an operator's usage while type inferrers are being invoked. |

### Structures

| Name | Description |
|------|-------------|
| [MLOperatorEdgeDescription](custom-operators/MLOperatorEdgeDescription.md) | Specifies the properties of an input or output edge of an operator. |
| [MLOperatorEdgeTypeConstraint](custom-operators/MLOperatorEdgeTypeConstraint.md) | Specifies constraints upon the types of edges supported in custom operator kernels and schema. |
| [MLOperatorSchemaEdgeDescription](custom-operators/MLOperatorSchemaEdgeDescription.md) | Specifies information about an input or output edge of an operator. |

#### GetInputEdgeDescription method

Gets the description of the specified input edge of the operator.

```cpp
void GetInputEdgeDescription(
    uint32_t inputIndex,
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

#### SetOutputEdgeDescription method

Sets the inferred type of an output edge.

```cpp
void SetOutputEdgeDescription(
    uint32_t outputIndex, 
    const MLOperatorEdgeDescription* edgeDescription)
```

### IMLOperatorTypeInferrer interface

Implemented by type inferrers to infer the types of an operator's output edges. Type inferrers must be provided when registering schema of custom operators if the **MLOperatorSchemaDescription** structure cannot express how output types are determined&mdash;for example, when an attribute of the operator determines the data type of one of that operator's outputs.

#### InferOutputTypes method

Called to infer types of an operator's output edges.

```cpp
void InferOutputTypes(
    IMLOperatorTypeInferenceContext* context)
```

### IMLOperatorShapeInferrer interface

Implemented by shape inferrers to infer shapes of an operator's output tensor edges. Shape inferrers may be provided when registering custom operator kernels to improve performance and to enable the kernel to query the shape of its output tensors when it is created and computed. Shape inferrers may also be provided when registering custom operator schema to improve model validation.

#### InferOutputShapes method

Called to infer shapes of an operator's output edges.

```cpp
void InferOutputShapes(
    IMLOperatorShapeInferenceContext* context)
```

### MLOperatorAttribute struct

Specifies the name and properties of an attribute of a custom operator. This is used when registering custom operator kernels and custom operator schema.

#### Fields

| Name     | Type                    | Description |
|----------|-------------------------|-------------|
| name     | char*                   | NULL-terminated UTF-8 string representing the name of the attribute in the associated operator type. |
| type     | MLOperatorAttributeType | The type of the attribute in the associated operator type. |
| required | bool                    | Whether the attribute is required in any model using the associated operator type. |

### MLOperatorAttributeNameValue struct

Specifies the name and value(s) of an attribute of a custom operator. This is used when registering custom operator kernels and custom operator schema.

#### Fields

| Name       | Type                    | Description |
|------------|-------------------------|-------------|
| name       | const char*             | NULL-terminated UTF-8 string representing the name of the attribute in the associated operator type. |
| type       | MLOperatorAttributeType | The type of the attribute in the associated operator type. |
| valueCount | uint32_t                | The number of elements in the attribute value. This must be 1, except for attributes which are of array types. |
| reserved   | const void*             |             |
| ints       | const int64_t*          | 64-bit integer value(s). Used when the type field is **MLOperatorAttributeType::Int** or **MLOperatorAttributeType::IntArray**. |
| strings    | const char* const*      | NULL-terminated UTF-8 string value(s). Used when the type field is **MLOperatorAttributeType::String** or **MLOperatorAttributeType::StringArray**. |
| floats     | const float*            | 32-bit floating point value(s). Used when the type field is **MLOperatorAttributeType::Float** or **MLOperatorAttributeType::FloatArray**. |

### MLOperatorSchemaDescription struct

Description of a custom operator schema used to register that schema.

#### Fields

| Name | Type | Description |
|------|------|-------------|
| name | const char* | NULL-terminated UTF-8 string representing the name of the operator. |
| operatorSetVersionAtLastChange | int32_t | The operator set version at which this operator was introduced or last changed. |
| inputs | const MLOperatorSchemaEdgeDescription* | An array containing the descriptions of the operator's input edges. |
| inputCount | uint32_t | The number of inputs of the operator. |
| outputs | const MLOperatorSchemaEdgeDescription* | An array containing the descriptions of the operator's output edges. |
| outputCount | uint32_t | The number of outputs of the operator. |
| typeConstraints | const MLOperatorEdgeTypeConstraint* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |
| typeConstraintCount | uint32_t | The number of type constraints provided. |
| attributes | const MLOperatorAttribute* | The set of attributes supported by the operator type. |
| attributeCount | uint32_t | The number of provided attributes. |
| defaultAttributes | const MLOperatorAttributeNameValue* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| defaultAttributeCount | uint32_t | The number of provided default attribute values. |

### MLOperatorSetId struct

Specifies the identity of an operator set.

#### Fields

| Name | Type | Description |
|------|------|-------------|
| domain | const char* | The domain of the operator, for example, "ai.onnx.ml", or an empty string for the ONNX domain. |
| version | int32_t | The version of the operator domain. |

### MLOperatorKernelOptions enum

Specifies options used when registering custom operator kernels.

#### Fields

| Name | Value | Description |
|------|-------|-------------|
| None | 0 | |
| AllowDynamicInputShapes | 1 | Specifies whether the shapes of input tensors are allowed to vary among invocations of an operator kernel instance. If this is not set, kernel instances may query input tensor shapes during creation, and front-load initialization work which depends on those shapes. Setting this may improve performance if shapes vary dynamically between inference operations, and the kernel implementation handles this efficiently. |

### MLOperatorExecutionType enum

Specifies whether a kernel uses the CPU or GPU for computation.

#### Fields

| Name | Value | Description |
|------|-------|-------------|
| Undefined | 0 | |
| Cpu | 1 | |
| D3D12 | 2 | |

### MLOperatorKernelDescription struct

Description of a custom operator kernel used to register that schema.

#### Fields

| Name | Type | Description |
|------|------|-------------|
| domain | const char* | NULL-terminated UTF-8 string representing the name of the operator's domain. |
| name | const char* | NULL-terminated UTF-8 string representing the name of the operator. |
| minimumOperatorSetVersion | int32_t | The minimum version of the operator sets for which this kernel is valid. The maximum version is inferred based on registrations of operator set schema for subsequent versions of the same domain. |
| executionType | MLOperatorExecutionType | Specifies whether a kernel uses the CPU or GPU for computation. |
| typeConstraints | const MLOperatorEdgeTypeConstraint* | An array of type constraints. Each constraint restricts input and outputs associated with a type label string to one or more edge types. |
| typeConstraintCount | uint32_t | The number of type constraints provided. |
| defaultAttributes | const MLOperatorAttributeNameValue* | The default values of attributes. These will be applied when the attributes are missing in a model containing the operator type. |
| defaultAttributeCount | uint32_t | The number of provided default attribute values. |
| options | MLOperatorKernelOptions | Options for the kernel which apply to all execution provider types. |
| executionOptions | uint32_t | Reserved for additional options. Must be 0. |

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