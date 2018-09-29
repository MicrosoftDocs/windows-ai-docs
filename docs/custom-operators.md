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
| [MLOperatorTensorDataType](custom-operators/MLOperatorTensorDataType.md) | Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type. |

### Interfaces

| Name | Description |
|------|-------------|
| [IMLOperatorAttributes](custom-operators/IMLOperatorAttributes.md) | Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers. |
| [IMLOperatorKernelCreationContext](custom-operators/IMLOperatorKernelCreationContext.md) | Provides information about an operator's usage while kernels are being created. |
| [IMLOperatorTensor](custom-operators/IMLOperatorTensor.md) | Representation of a tensor used during computation of custom operator kernels. |
| [IMLOperatorTensorShapeDescription](custom-operators/IMLOperatorTensorShapeDescription.md) | Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag. |

### Structures

| Name | Description |
|------|-------------|
| [MLOperatorEdgeDescription](custom-operators/MLOperatorEdgeDescription.md) | Specifies the properties of an input or output edge of an operator. |

#### GetData method

Returns a pointer to byte-addressable memory for the tensor. This may be used when **IsDataInterface** returns false, because the kernel was registered using **MLOperatorExecutionType::Cpu**. The data size is derived from the tensor's shape.  It is fully packed in memory.

```cpp
void* GetData()
```

#### GetDataInterface method

Gets an interface pointer for the tensor. This may be used when **IsDataInterface** returns true, because the kernel was registered using **MLOperatorExecutionType::D3D12**. The *dataInterface* object supports the [ID3D12Resource interface](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12resource), and is a GPU buffer.

```cpp
void GetDataInterface(
    _COM_Outptr_result_maybenull_ IUnknown** dataInterface)
```

### IMLOperatorKernelContext interface

Provides information about an operator's usage while kernels are being computed.

#### GetInputTensor method

Gets the input tensor of the operator at the specified index. This sets the tensor to **nullptr** for optional inputs which do not exist. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensor(
    uint32_t inputIndex, 
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

#### GetOutputTensor method

Gets the output tensor of the operator at the specified index. This sets the tensor to **nullptr** for optional outputs which do not exist. If the operator kernel was registered without a shape inference method, then the overload of **GetOutputTensor** which consumes the tensor's shape must be called instead. Returns an error if the output at the specified index is not a tensor.

```cpp
void GetOutputTensor(
    uint32_t outputIndex, 
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

#### GetOutputTensor method

Gets the output tensor of the operator at the specified index, while declaring its shape. This returns **nullptr** for optional outputs which do not exist. If the operator kernel was registered with a shape inference method, then the overload of **GetOutputTensor** which doesn't consume a shape may also be called. Returns an error if the output at the specified index is not a tensor.

```cpp
void GetOutputTensor(
    uint32_t outputIndex,
    uint32_t dimensionCount,
    _In_reads_(dimensionCount) const uint32_t* dimensionSizes,
    _COM_Outptr_result_maybenull_ IMLOperatorTensor** tensor)
```

#### AllocateTemporaryData method

Allocates temporary data which will be usable as intermediate memory for the duration of a call to **IMLOperatorKernel::Compute**. This may be used by kernels registered using **MLOperatorExecutionType::D3D12**. The data object supports the **ID3D12Resource** interface, and is a GPU buffer.

```cpp
void AllocateTemporaryData(
    size_t size, 
    _COM_Outptr_ IUnknown** data)
```

#### GetExecutionInterface method

Returns an object whose supported interfaces vary based on the kernel type. For kernels registered with **MLOperatorExecutionType::Cpu**, *executionObject* will be set to **nullptr**. For kernels registered with **MLOperatorExecutionType::D3D12**, *executionObject* will support the **ID3D12GraphicsCommandList** interface. This may be a different object than was provided to **IMLOperatorKernelCreationContext::GetExecutionInterface** when the kernel instance was created.

```cpp
void GetExecutionInterface(
    _COM_Outptr_result_maybenull_ IUnknown** executionObject)
```

### IMLOperatorKernel interface

Implemented by custom operator kernels. A factory which creates interfaces of this interface is supplied when registering custom operator kernels using **IMLOperatorKernelFactory::RegisterOperatorKernel**.

#### Compute method

Computes the outputs of the kernel. The implementation of this method should be thread-safe. The same instance of the kernel may be computed simultaneously on different threads.

```cpp
void Compute(
    IMLOperatorKernelContext* context)
```

### MLOperatorParameterOptions enum

Specifies option flags of input and output edges of operators. These options are used while defining custom operator schema.

#### Fields

| Name     | Value | Description                                        |
|----------|-------|----------------------------------------------------|
| Single   | 0     | There is a single instance of the input or output. |
| Optional | 1     | The input or output may be omitted.                |
| Variadic | 2     | The number of instances of the operator is variable. Variadic parameters must be last among the set of inputs or outputs.             |

### MLOperatorSchemaEdgeTypeFormat enum

Specifies the manner in which types of input and output edges are described. This is used within **MLOperatorSchemaEdgeDescription** while defining custom operator schema.

#### Fields

| Name            | Value | Description |
|-----------------|-------|-------------|
| EdgeDescription | 0     | The type is defined using **MLOperatorEdgeDescription**.          |
| Label           | 1     | The type is defined by a type string constructed as in ONNX operator schema.                   |

### MLOperatorSchemaEdgeDescription struct

Specifies information about an input or output edge of an operator. This is used while defining custom operator schema.

#### Fields

| Name            | Type                           | Description |
|-----------------|--------------------------------|-------------|
| options         | MLOperatorParameterOptions     | Options of the parameter, including whether it is optional or variadic.                    |
| typeFormat      | MLOperatorSchemaEdgeTypeFormat | The manner in which the type constraints and type mapping are defined.                        |
| reserved        | void*                          |             |
| typeLabel       | char*                          | A type label string constructed as in ONNX operator schema. For example, "T". This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::Label**.     |
| edgeDescription | MLOperatorEdgeDescription      |  A structure describing type support. This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::EdgeDescription**.             |

### MLOperatorEdgeTypeConstraint struct

Specifies constraints upon the types of edges supported in custom operator kernels and schema. The provided type label string corresponds to type labels in the ONNX specification for the same operator. For custom schema, it corresponds to type labels specified within **MLOperatorSchemaEdgeDescription** when registering the operator's schema.

#### Fields

| Name             | Type                       | Description |
|------------------|----------------------------|-------------|
| typeLabel        | char*                      | The label of the type for which the constraint is being defined. This is constructed as in ONNX operator schema. For example, "T".                                             |
| allowedTypes     | MLOperatorEdgeDescription* | The set of allowed types for the constraint.                                                   |
| allowedTypeCount | uint32_t                   |             |

### IMLOperatorShapeInferenceContext interface

Provides information about an operator's usage while shape inferrers are being invoked.

#### GetInputCount method

Gets the number of inputs to the operator.

```cpp
uint32_t GetInputCount()
```

#### GetOutputCount method

Gets the number of outputs to the operator.

```cpp
uint32_t GetOutputCount()
```

#### IsInputValid method

Returns true if an input to the operator is valid. This always returns true except for optional inputs and invalid indices.

```cpp
bool IsInputValid(
    uint32_t inputIndex)
```

#### IsOutputValid method

Returns true if an output to the operator is valid. This always returns true except for optional outputs and invalid indices.

```cpp
bool IsOutputValid(
    uint32_t outputIndex)
```

#### GetInputEdgeDescription method

Gets the description of the specified input edge of the operator.

```cpp
void GetInputEdgeDescription(
    uint32_t inputIndex,
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

#### GetInputTensorDimensionCount method

Gets the number of dimensions of a tensor output of the operator.

```cpp
void GetInputTensorDimensionCount(
    uint32_t inputIndex,
    _Out_ uint32_t* dimensionCount)
```

#### GetInputTensorShape method

Gets the sizes of dimensions of an input tensor of the operator. Returns an error if the input at the specified index is not a tensor.

```cpp
void GetInputTensorShape(
    uint32_t inputIndex,
    uint32_t dimensionCount,
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

#### SetOutputTensorShape method

Sets the inferred shape of an output tensor. Returns an error if the output at the specified index is not a tensor.

```cpp
void SetOutputTensorShape(
    uint32_t outputIndex, 
    uint32_t dimensionCount, 
    const uint32_t* dimensions)
```

### IMLOperatorTypeInferenceContext interface

Provides information about an operator's usage while type inferrers are being invoked.

#### GetInputCount method

Gets the number of inputs to the operator.

```cpp
uint32_t GetInputCount()
```

#### GetOutputCount method

Gets the number of outputs to the operator.

```cpp
uint32_t GetOutputCount()
```

#### IsInputValid method

Returns true if an input to the operator is valid. This always returns true except for optional inputs.

```cpp
bool IsInputValid(
    uint32_t inputIndex)
```

#### IsOutputValid method

Returns true if an output to the operator is valid. This always returns true except for optional outputs.

```cpp
bool IsOutputValid(
    uint32_t outputIndex)
```

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