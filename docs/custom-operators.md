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

### MLOperatorAttributeType enum

Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.

#### Fields

| Name        | Value | Description                            |
|-------------|-------|----------------------------------------|
| Undefined   | 0     | Undefined (unused).                    |
| Float       | 2     | 32-bit floating point.                 |
| Int         | 3     | 64-bit integer.                        |
| String      | 4     | String value.                          |
| FloatArray  | 7     | Array of 32-bit floating point values. |
| IntArray    | 8     | Array of 64-bit integer values.        |
| StringArray | 9     | Array of string values.                |

### MLOperatorTensorDataType enum

Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type.

#### Fields

| Name       | Value | Description                             |
|------------|-------|-----------------------------------------|
| Undefined  | 0     | Undefined (unused).                     |
| Float      | 1     | IEEE 32-bit floating point.             |
| UInt8      | 2     | 8-bit unsigned integer.                 |
| Int8       | 3     | 8-bit signed integer.                   |
| UInt16     | 4     | 16-bit unsigned integer.                |
| Int16      | 5     | 16-bit signed integer.                  |
| Int32      | 6     | 32-bit signed integer.                  |
| Int64      | 7     | 64-bit signed integer.                  |
| String     | 8     | String (unsupported).                   |
| Bool       | 9     | 8-bit boolean. Values other than zero and one result in undefined behavior. |
| Float16    | 10    | IEEE 16-bit floating point.             |
| Double     | 11    | 64-bit double-precision floating point. |
| UInt32     | 12    | 32-bit unsigned integer.                |
| UInt64     | 13    | 64-bit unsigned integer.                |
| Complex64  | 14    | 64-bit complex type (unsupported).      |
| Complex128 | 15    | 128-bit complex type (unsupported).     |

### MLOperatorEdgeType enum

Specifies the types of an input or output edge of an operator.

#### Fields

| Name      | Value | Description |
|-----------|-------|-------------|
| Undefined | 0     |             |
| Tensor    | 1     |             |

### MLOperatorEdgeDescription struct

Specifies the properties of an input or output edge of an operator.

#### Fields

| Name           | Type                     | Description           |
|----------------|--------------------------|-----------------------|
| edgeType       | MLOperatorEdgeType       | The type of the edge. |
| reserved       | uint64_t                 |                       |
| tensorDataType | MLOperatorTensorDataType | The data type of a tensor. Used when **edgeType** is set to **Tensor**. |

### IMLOperatorAttributes interface

Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers.

#### GetAttributeElementCount method

Gets the count of elements in an attribute. This may be used to determine if an attribute exists, and to determine the count of elements within an attribute of an array type.

```cpp
void GetAttributeElementCount(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    _Out_ uint32_t* elementCount)
```

#### GetAttribute method

Gets the value of an attribute element which is of a numeric type. For attributes which are of array types, this method queries an individual element within the attribute at the specified index.

```cpp
void GetAttribute(
    _In_z_ const char* name,
    MLOperatorAttributeType type,
    uint32_t elementCount,
    size_t elementByteSize,
    _Out_writes_bytes_(elementCount * elementByteSize) void* value)
```

#### GetStringAttributeElementLength method

Gets the length of an attribute element which is of a string type. For attributes which are string arrays, this method queries the size of an individual element within the attribute at the specified index. The string is in UTF-8 format.  The size includes the null termination character.

```cpp
void GetStringAttributeElementLength(
    _In_z_ const char* name,
    uint32_t elementIndex,
    _Out_ uint32_t* attributeElementByteSize)
```

#### GetStringAttributeElement method

Gets the value of an attribute element which is of a string type. For attributes which are string arrays, this method queries the value of an individual element within the attribute at the specified index. The string is in UTF-8 format. The size includes the null termination character.

```cpp
void GetStringAttributeElement(
    _In_z_ const char* name,
    uint32_t elementIndex,
    uint32_t attributeElementByteSize,
    _Out_writes_(uint32_t) char* attributeElement)
```

### IMLOperatorTensorShapeDescription interface

Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag.

#### GetInputTensorDimensionCount method

Gets the number of dimensions of a tensor input of the operator. Returns an error if the input at the specified index is not a tensor.

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

#### HasOutputShapeDescription method

Returns true if output shapes may be queried using **GetOutputTensorDimensionCount** and **GetOutputTensorShape**. This is true if the kernel was registered with a shape inferrer.

```cpp
bool HasOutputShapeDescription()
```

#### GetOutputTensorDimensionCount method

Gets the number of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor.

```cpp
GetOutputTensorDimensionCount(
    uint32_t outputIndex, 
    _Out_ uint32_t* dimensionCount)
```

#### GetOutputTensorShape method

Gets the sizes of dimensions of a tensor output of the operator. Returns an error if the output at the specified index is not a tensor.

```cpp
GetOutputTensorShape(
    uint32_t outputIndex, 
    uint32_t dimensionCount, 
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

### IMLOperatorKernelCreationContext interface

Provides information about an operator's usage while kernels are being created.

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
bool IsInputValid(uint32_t inputIndex)
```

#### IsOutputValid method

Returns true if an output to the operator is valid. This always returns true except for optional outputs.

```cpp
bool IsOutputValid(uint32_t outputIndex)
```

#### GetInputEdgeDescription method

Gets the description of the specified input edge of the operator.

```cpp
void GetInputEdgeDescription(
    uint32_t inputIndex, 
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

#### GetOutputEdgeDescription method

Gets the description of the specified output edge of the operator.

```cpp
void GetOutputEdgeDescription(
    uint32_t outputIndex, 
    _Out_ MLOperatorEdgeDescription* edgeDescription)
```

#### HasTensorShapeDescription method

Returns true if the description of input and output shapes connected to operator edges may be queried using **GetTensorShapeDescription**. This returns true unless the operator was registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag.

```cpp
bool HasTensorShapeDescription()
```

#### GetTensorShapeDescription method

Gets the description of input and output shapes connected to operator edges.

```cpp
void GetTensorShapeDescription(
    _COM_Outptr_ IMLOperatorTensorShapeDescription** shapeDescription)
```

#### GetExecutionInterface method

Returns an object whose supported interfaces vary based on the kernel type. For kernels registered with **MLOperatorExecutionType::Cpu**, *executionObject* will be set to **nullptr**. For kernels registered with **MLOperatorExecutionType::D3D12**, *executionObject* will support the [ID3D12GraphicsCommandList](https://docs.microsoft.com/windows/desktop/api/d3d12/nn-d3d12-id3d12graphicscommandlist) interface.

```cpp
void GetExecutionInterface(
    _COM_Outptr_result_maybenull_ IUnknown** executionObject)
```

### IMLOperatorTensor interface

Representation of a tensor used during computation of custom operator kernels.

#### GetDimensionCount method

Gets the number of dimensions in the tensor.  This may be zero.

```cpp
uint32_t GetDimensionCount()
```

#### GetShape method

Gets the size of dimensions in the tensor.

```cpp
void GetShape(
    uint32_t dimensionCount,
    _Out_writes_(dimensionCount) uint32_t* dimensions)
```

#### GetTensorDataType method

Gets the data type of the tensor.

```cpp
MLOperatorTensorDataType GetTensorDataType()
```

#### IsCpuData method

Indicates whether the memory used by the tensor is CPU-addressable. This is true when kernels are registered using **MLOperatorExecutionType::Cpu**.

```cpp
bool IsCpuData()
```

#### IsDataInterface method

Whether the contents of the tensor are represented by an interface type, or byte-addressable memory. This returns true when kernels are registered using **MLOperatorExecutionType::D3D12**.

```cpp
bool IsDataInterface()
```

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

[!INCLUDE [help](includes/get-help.md)]