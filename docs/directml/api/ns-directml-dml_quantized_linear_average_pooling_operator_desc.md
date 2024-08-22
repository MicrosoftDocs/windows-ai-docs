---
UID: NS:directml.DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC
title: DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC structure
description: Averages quantized values across the elements within the sliding window over the input tensor. This operator is mathematically equivalent to dequantizing the inputs, then performing average pooling, and then quantizing the output.
ms.topic: reference
tech.root: directml
ms.date: 08/21/2024
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
 - DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC
 - directml/DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC
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
 - DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC
---

# DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC structure (directml.h)

Averages quantized values across the elements within the sliding window over the input tensor. This operator is mathematically equivalent to dequantizing the inputs, then performing average pooling, and then quantizing the output.

**Dequantize function**

```
f(Input, Scale, ZeroPoint) = (Input - ZeroPoint) * Scale
```

**Quantize function**

```
f(Input, Scale, ZeroPoint) = clamp(round(Input / Scale) + ZeroPoint, Min, Max)
```

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.13 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_QUANTIZED_LINEAR_AVERAGE_POOLING_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* InputScaleTensor;
    _Maybenull_ const DML_TENSOR_DESC* InputZeroPointTensor;
    const DML_TENSOR_DESC* OutputScaleTensor;
    _Maybenull_ const DML_TENSOR_DESC* OutputZeroPointTensor;
    const DML_TENSOR_DESC* OutputTensor;
    UINT DimensionCount;
    _Field_size_(DimensionCount) const UINT* Strides;
    _Field_size_(DimensionCount) const UINT* WindowSize;
    _Field_size_(DimensionCount) const UINT* StartPadding;
    _Field_size_(DimensionCount) const UINT* EndPadding;
    _Field_size_(DimensionCount) const UINT* Dilations;
    BOOL IncludePadding;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An input tensor of *Sizes* `{ BatchCount, ChannelCount, Height, Width }` for 4D, and `{ BatchCount, ChannelCount, Depth, Height, Weight }` for 5D.

`InputScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *InputTensor* scale data. The expected dimensions of *InputScaleTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, ChannelCount, 1, 1 }` if per-channel quantization is required. These scale values are used for dequantizing the *InputTensor* values.

> [!NOTE]
> A scale value of 0 results in undefined behavior.

`InputZeroPointTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the *InputTensor* zero point data. The expected dimensions of *InputZeroPointTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, ChannelCount, 1, 1 }` if per-channel quantization is required. These zero point values are used for dequantizing the *InputTensor* values.

`OutputScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the *OutputTensor* scale data. The expected dimensions of *OutputScaleTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, ChannelCount, 1, 1 }` if per-channel quantization is required. These scale values are used for quantizing the *OutputTensor* values.

> [!NOTE]
> A scale value of 0 results in undefined behavior.

`OutputZeroPointTensor`

Type: _Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the *OutputTensor* zero point data. The expected dimensions of *OutputZeroPointTensor* are `{ 1, 1, 1, 1 }` if per-tensor quantization is required, or `{ 1, ChannelCount, 1, 1 }` if per-channel quantization is required. This zero point value is used for quantizing the *OutputTensor* values.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A description of the output tensor. The sizes of the output tensor can be computed as follows.

```cpp
OutputTensor->Sizes[0] = InputTensor->Sizes[0];
OutputTensor->Sizes[1] = InputTensor->Sizes[1];

for (UINT i = 0; i < DimensionCount; ++i) {
  UINT PaddedSize = InputTensor->Sizes[i + 2] + StartPadding[i] + EndPadding[i];
  OutputTensor->Sizes[i + 2] = (PaddedSize - WindowSizes[i]) / Strides[i] + 1;
}
```

`DimensionCount`

Type: [**UINT**](/windows/desktop/winprog/windows-data-types)

The number of spatial dimensions of the input tensor *InputTensor*, which also corresponds to the number of dimensions of the sliding window *WindowSize*. This value also determines the size of the *Strides*, *StartPadding*, and *EndPadding* arrays. It should be set to 2 when *InputTensor* is 4D, and 3 when it's a 5D tensor.

`Strides`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The strides for the sliding window dimensions of sizes `{ Height, Width }` when the *DimensionCount* is set to 2, or `{ Depth, Height, Width }` when set to 3.

`WindowSize`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The dimensions of the sliding window in `{ Height, Width }` when *DimensionCount* is set to 2, or `{ Depth, Height, Width }` when set to 3.

`StartPadding`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The number of padding elements to be applied to the beginning of each spatial dimension of the input tensor *InputTensor*. The values are in `{ Height, Width }` when *DimensionCount* is set to 2, or `{ Depth, Height, Width }` when set to 3.

`EndPadding`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The number of padding elements to be applied to the end of each spatial dimension of the input tensor *InputTensor*. The values are in `{ Height, Width }` when *DimensionCount* is set to 2, or `{ Depth, Height, Width }` when set to 3.

`Dilations`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The values for each spatial dimension of the input tensor *InputTensor* by which an element within the sliding window is selected for every element of that value. The values are in `{ Height, Width }` when *DimensionCount* is set to 2, or `{ Depth, Height, Width }` when set to 3.

`IncludePadding`

Type: <b><a href="/windows/desktop/WinProg/windows-data-types">BOOL</a></b>

Indicates whether to include the padding elements around the spatial edges when calculating the average value across all elements within the sliding window. When the value is set to **FALSE**, the padding elements are not counted as part of the divisor value of the averaging calculation.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_2**.

## Tensor constraints
* *InputTensor* and *OutputTensor* must have the same *DimensionCount*.
* *InputTensor* and *InputZeroPointTensor* must have the same *DataType*.
* *OutputTensor* and *OutputZeroPointTensor* must have the same *DataType*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 4 to 5 | INT8, UINT8 |
| InputScaleTensor | Input | 1 to 5 | FLOAT32 |
| InputZeroPointTensor | Optional input | 1 to 5 | INT8, UINT8 |
| OutputScaleTensor | Input | 1 to 5 | FLOAT32 |
| OutputZeroPointTensor | Optional input | 1 to 5 | INT8, UINT8 |
| OutputTensor | Output | 4 to 5 | INT8, UINT8 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
