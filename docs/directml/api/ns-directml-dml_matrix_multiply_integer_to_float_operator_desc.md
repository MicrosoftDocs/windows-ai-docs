---
UID: NS:directml.DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC
title: DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC structure
description: Performs a matrix multiplication function on integer input tensor data, and produces floating point output.
ms.topic: reference
tech.root: directml
ms.date: 01/08/2024
req.header: directml.h
req.include-header: 
req.target-type: Windows
req.target-min-winverclnt: 
req.target-min-winversvr: 
req.kmdf-ver: 
req.umdf-ver: 
req.ddi-compliance: 
req.unicode-ansi: 
req.idl: 
req.max-support: 
req.namespace: 
req.assembly: 
req.type-library: 
req.lib: 
req.dll: 
req.irql: 
targetos: Windows
req.typenames: 
req.redist: 
f1_keywords:
 - DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC
 - directml/DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC
dev_langs:
 - c++
topic_type:
 - APIRef
 - kbSyntax
api_type:
 - HeaderDef
api_location:
 - DirectML.h
api_name:
 - DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC
---

# DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC structure (directml.h)

Performs a matrix multiplication function on integer input tensor data, and produces floating point output.

This operator requires the matrix multiply input tensors to be 4D, which are formatted as `{ BatchCount, ChannelCount, Height, Width }`. The matrix multiply operator will perform BatchCount * ChannelCount number of independent matrix multiplications.

For example, if *ATensor* has *Sizes* of `{ BatchCount, ChannelCount, M, K }`, and *BTensor* has *Sizes* of `{ BatchCount, ChannelCount, K, N }`, and *OutputTensor* has *Sizes* of `{ BatchCount, ChannelCount, M, N }`, then the matrix multiply operator will perform BatchCount * ChannelCount independent matrix multiplications of dimensions {M,K} x {K,N} = {M,N}. 

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.13 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_MATRIX_MULTIPLY_INTEGER_TO_FLOAT_OPERATOR_DESC
{
    const DML_TENSOR_DESC* ATensor;
    const DML_TENSOR_DESC* AScaleTensor;
    _Maybenull_ const DML_TENSOR_DESC* AZeroPointTensor;
    const DML_TENSOR_DESC* BTensor;
    const DML_TENSOR_DESC* BScaleTensor;
    _Maybenull_ const DML_TENSOR_DESC* BZeroPointTensor;
    _Maybenull_ const DML_TENSOR_DESC* BiasTensor;
    const DML_TENSOR_DESC* OutputTensor;
};
```

## Members

`ATensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *A* data. This tensor's dimensions should be `{ BatchCount, ChannelCount, M, K }`.

`AScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *ATensor* scale data. The expected dimensions of *AScaleTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, M, 1 }` if per-row quantization is required. These scale values are used for dequantizing the *ATensor* values.

`AZeroPointTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the *ATensor* zero point data. The expected dimensions of *AZeroPointTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, M, 1 }` if per-row quantization is required. These zero point values are used for dequantizing the *ATensor* values.

`BTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *B* data. This tensor's dimensions should be `{ BatchCount, ChannelCount, K, N }`.

`BScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *BTensor* scale data. The expected dimensions of *BScaleTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, 1, N }` if per-column quantization is required. These scale values are used for dequantizing the *BTensor* values.

`BZeroPointTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the *BTensor* zero point data. The expected dimensions of *BZeroPointTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, 1, N }` if per-column quantization is required. These zero point values are used for dequantizing the *BTensor* values.

`OutputScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *OutputTensor* scale data. The expected dimensions of *OutputScaleTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, M, 1 }` if per-row quantization is required. This scale value is used for dequantizing the *OutputTensor* values.

`OutputZeroPointTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the *OutputTensor* zero point data. The expected dimensions of *OutputZeroPointTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, 1, M, 1 }` if per-row quantization is required. This zero point value is used for dequantizing the *OutputTensor* values.

`BiasTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the bias data. If provided, this tensor's *Sizes* should match output size `{ BatchCount, ChannelCount, M, N }`.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor with which to write the results to. This tensor's dimensions are `{ BatchCount, ChannelCount, M, N }`.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_2**.

## Tensor constraints
* *AScaleTensor*, *AZeroPointTensor*, *BScaleTensor*, and *BZeroPointTensor* must have the same *DimensionCount*.
* *ATensor*, *BiasTensor*, *BTensor*, and *OutputTensor* must have the same *DimensionCount*.
* *BiasTensor* and *OutputTensor* must have the same *Sizes*.
* *ATensor*, *AZeroPointTensor*, *BTensor*, and *BZeroPointTensor* must have the same *DataType*.
* *AScaleTensor*, *BiasTensor*, *BScaleTensor*, and *OutputTensor* must have the same *DataType*.

## Tensor support
| Tensor | Kind | Dimensions | Supported dimension counts | Supported data types |
| ------ | ---- | ---------- | -------------------------- | -------------------- |
| ATensor | Input | { [BatchCount], [ChannelCount], M, K } | 2 to 4 | INT32, INT16, INT8, UINT32, UINT16, UINT8 |
| AScaleTensor | Input | { AScaleDimensions[] } | 1 to 4 | FLOAT32, FLOAT16 |
| AZeroPointTensor | Optional input | { [1], [1], AZeroPointCount, [1] } | 1 to 4 | INT32, INT16, INT8, UINT32, UINT16, UINT8 |
| BTensor | Input | { [BatchCount], [ChannelCount], K, N } | 2 to 4 | INT32, INT16, INT8, UINT32, UINT16, UINT8 |
| BScaleTensor | Input | { BScaleDimensions[] } | 1 to 4 | FLOAT32, FLOAT16 |
| BZeroPointTensor | Optional input | { [1], [1], [1], BZeroPointCount } | 1 to 4 | INT32, INT16, INT8, UINT32, UINT16, UINT8 |
| BiasTensor | Optional input | { [BatchCount], [ChannelCount], M, N } | 2 to 4 | FLOAT32, FLOAT16 |
| OutputTensor | Output | { [BatchCount], [ChannelCount], M, N } | 2 to 4 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
