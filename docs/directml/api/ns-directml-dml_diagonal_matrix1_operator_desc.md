---
UID: NS:directml.DML_DIAGONAL_MATRIX1_OPERATOR_DESC
title: DML_DIAGONAL_MATRIX1_OPERATOR_DESC structure
description: Generates an identity-like matrix with ones (or other explicit value) along the given diagonal span, with other elements being filled with either the input values or zeros (if no *InputTensor* is passed).
helpviewer_keywords: ["DML_DIAGONAL_MATRIX1_OPERATOR_DESC","DML_DIAGONAL_MATRIX1_OPERATOR_DESC structure","direct3d12.dml_diagonal_matrix1_operator_desc","directml/DML_DIAGONAL_MATRIX1_OPERATOR_DESC"]
ms.topic: reference
tech.root: directml
ms.date: 07/20/2022
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
 - DML_DIAGONAL_MATRIX1_OPERATOR_DESC
 - directml/DML_DIAGONAL_MATRIX1_OPERATOR_DESC
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
 - DML_DIAGONAL_MATRIX1_OPERATOR_DESC
---

# DML_DIAGONAL_MATRIX1_OPERATOR_DESC structure (directml.h)

Generates an identity-like matrix with ones (or other explicit value) along the given diagonal span, with other elements being filled with either the input values or zeros (if no *InputTensor* is passed). The diagonal values may be shifted anywhere between *DiagonalFillBegin* and *DiagonalFillEnd*, where a value greater than zero shifts all values to the right, and less than zero shifts them to the left. This generator operator is useful for models to avoid storing a large constant tensor. Any leading dimensions before the last two are treated as a batch count, meaning that the tensor is treated as stack of 2D matrices. This operator performs the following pseudocode:

```
// Given each current coordinate's x, figure out the corresponding x on the top (0th) row.
// Then fill it with Value if that x coordinate is either within the fill bounds, or outside
// the fill bounds when inverted. Otherwise, use the original input value or zero.

for each coordinate in OutputTensor
    topX = coordinate.x - coordinate.y
    useValue = (DiagonalFillEnd >= DiagonalFillBegin) ^ (topX >= DiagonalFillBegin) ^ (topX < DiagonalFillEnd)
    OutputTensor[coordinate] = if useValue then Value
                               else if InputTensor not null then InputTensor[coordinate]
                               else 0
endfor
```

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.9 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_DIAGONAL_MATRIX1_OPERATOR_DESC
{
    _Maybenull_ const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    DML_TENSOR_DATA_TYPE ValueDataType;
    DML_SCALAR_UNION Value;
    INT DiagonalFillBegin;
    INT DiagonalFillEnd;
};
```

## Members

`InputTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional input tensor.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The tensor to write the results to. The dimensions are `{ Batch1, Batch2, OutputHeight, OutputWidth }`. The height and width don't need to be square.

`ValueDataType`

Type: [**DML_TENSOR_DATA_TYPE**](/windows/win32/api/directml/ne-directml-dml_tensor_data_type)

The data type of the *Value* member, which must match *OutputTensor::DataType*.

`Value`

Type: [**DML_SCALAR_UNION**](/windows/win32/api/directml/ns-directml-dml_scalar_union)

A constant value to fill the output, with *ValueDataType* determining how to interpret the member.

`DiagonalFillBegin`

Type: [**INT**](/windows/win32/winprog/windows-data-types)

The lower limit (beginning inclusive) of the range to fill with *Value*. If the beginning and ending are inverted (begin > end), then the fill will be reversed.

`DiagonalFillEnd`

Type: [**INT**](/windows/win32/winprog/windows-data-types)

The upper limit (ending exclusive) of the range to fill with *Value*. If the beginning and ending are inverted (begin > end), then the fill will be reversed.

## Examples

Default identity matrix:

```
InputTensor: none
ValueDataType: FLOAT32
Value: 7
DiagonalFillBegin: 0
DiagonalFillEnd: 1
OutputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[1, 0, 0, 0, 0],
     [0, 1, 0, 0, 0],
     [0, 0, 1, 0, 0],
     [0, 0, 0, 1, 0]]
```

Fill a 3-element wide diagonal strip:

```
InputTensor: none
ValueDataType: FLOAT32
Value: 7
DiagonalFillBegin: 0
DiagonalFillEnd: 3
OutputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[7, 7, 7, 0, 0],
     [0, 7, 7, 7, 0],
     [0, 0, 7, 7, 7],
     [0, 0, 0, 7, 7]]
```

Keep top-right diagonal half (the strictly upper triangle), zeroing the bottom left.

```
InputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[4, 7, 3, 7, 9],
     [1, 2, 8, 6, 9],
     [9, 4, 1, 8, 7],
     [4, 3, 4, 2, 4]]
ValueDataType: FLOAT32
Value: 0
DiagonalFillBegin: INT32_MIN
DiagonalFillEnd: 1
OutputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[0, 7, 3, 7, 9],
     [0, 0, 8, 6, 9],
     [0, 0, 0, 8, 7],
     [0, 0, 0, 0, 4]]
```

Keep only the values along the diagonal, zeroing all others.

```
InputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[4, 7, 3, 7, 9],
     [1, 2, 8, 6, 9],
     [9, 4, 1, 8, 7],
     [4, 3, 4, 2, 4]]
ValueDataType: FLOAT32
Value: 0
DiagonalFillBegin: 1
DiagonalFillEnd: 0
OutputTensor: (Sizes:{4,5} DataType:FLOAT32)
    [[4, 0, 0, 0, 0],
     [0, 2, 0, 0, 0],
     [0, 0, 1, 0, 0],
     [0, 0, 0, 2, 0]]
```

## Remarks

This operator is equivalent to [DML_DIAGONAL_MATRIX_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_diagonal_matrix_operator_desc) when *DiagonalFillBegin* is set to *DML_OPERATOR_DIAGONAL_MATRIX::Offset*, and *DiagonalFillEnd* is set to *DML_OPERATOR_DIAGONAL_MATRIX::Offset* + 1.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_5_1**.

## Tensor constraints
*InputTensor* and *OutputTensor* must have the same *DataType* and *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Optional input | 2 to 4 | FLOAT64, FLOAT32, FLOAT16, INT64, INT32, INT16, INT8, UINT64, UINT32, UINT16, UINT8 |
| OutputTensor | Output | 2 to 4 | FLOAT64, FLOAT32, FLOAT16, INT64, INT32, INT16, INT8, UINT64, UINT32, UINT16, UINT8 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
