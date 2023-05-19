---
UID: NS:directml.DML_RESAMPLE_GRAD1_OPERATOR_DESC
title: DML_RESAMPLE_GRAD1_OPERATOR_DESC
description: Computes backpropagation gradients for [DML_RESAMPLE2_OPERATOR_DESC](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).
helpviewer_keywords: ["DML_RESAMPLE_GRAD1_OPERATOR_DESC","DML_RESAMPLE_GRAD1_OPERATOR_DESC structure","direct3d12.dml_resample_grad1_operator_desc","directml/DML_RESAMPLE_GRAD1_OPERATOR_DESC"]
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
 - DML_RESAMPLE_GRAD1_OPERATOR_DESC
 - directml/DML_RESAMPLE_GRAD1_OPERATOR_DESC
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
 - DML_RESAMPLE_GRAD1_OPERATOR_DESC
---

# DML_RESAMPLE_GRAD1_OPERATOR_DESC (directml.h)

Computes backpropagation gradients for [DML_RESAMPLE2_OPERATOR_DESC](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

**DML_RESAMPLE2_OPERATOR_DESC** rescales arbitrary dimensions of the input tensor using either nearest-neighbor sampling or bilinear interpolation. Given an *InputGradientTensor* with the same sizes as the *output* of an equivalent **DML_RESAMPLE2_OPERATOR_DESC**, this operator produces an *OutputGradientTensor* with the same sizes as the *input* of the **DML_RESAMPLE2_OPERATOR_DESC**.

As an example, consider a **DML_RESAMPLE2_OPERATOR_DESC** that performs a nearest-neighbor scaling of 1.5x in the width, and 0.5x in the height:

```
InputTensor           OutputTensor
[[1, 2],   Resample    [1, 1, 2]
 [3, 4]]      -->      
```

Notice how the 0th element of the input tensor (with value 1) contributes to two elements in the output; the 1st element (with value 2) contributes to one element in the output; and the 2nd and 3rd elements (with values 3 and 4) don't contribute to any elements of the output.

The corresponding **DML_RESAMPLE_GRAD1_OPERATOR_DESC** would perform the following:

```
InputGradientTensor           OutputGradientTensor
    [4, 5, 6]      ResampleGrad    [[9, 6],
                       -->          [0, 0]]
```

Notice that the values in the *OutputGradientTensor* represent the weighted contributions of that element to the *OutputTensor* during the original **DML_RESAMPLE2_OPERATOR_DESC** operator.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.9 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_RESAMPLE_GRAD1_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputGradientTensor;
    const DML_TENSOR_DESC* OutputGradientTensor;
    DML_INTERPOLATION_MODE InterpolationMode;
    DML_AXIS_DIRECTION RoundingDirection;
    UINT DimensionCount;
    _Field_size_(DimensionCount) const FLOAT* Scales;
    _Field_size_(DimensionCount) const FLOAT* InputPixelOffsets;
    _Field_size_(DimensionCount) const FLOAT* OutputPixelOffsets;
};
```

## Members

`InputGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The incoming gradient tensor. This is typically obtained from the output of backpropagation of a preceding layer. Typically this tensor would have the same sizes as the *output* of the corresponding [DML_RESAMPLE2_OPERATOR_DESC](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc) in the forward pass.

`OutputGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An output tensor containing the backpropagated gradients. Typically this tensor would have the same sizes as the *input* of the corresponding [DML_RESAMPLE2_OPERATOR_DESC](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc) in the forward pass.

`InterpolationMode`

Type: [**DML_INTERPOLATION_MODE**](/windows/win32/api/directml/ne-directml-dml_interpolation_mode)

See [DML_RESAMPLE2_OPERATOR_DESC::InterpolationMode](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

`RoundingDirection`

Type: [**DML_AXIS_DIRECTION**](/windows/win32/api/directml/ne-directml-dml_axis_direction)

See [DML_RESAMPLE2_OPERATOR_DESC::RoundingDirection](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

`DimensionCount`

Type: [**UINT**](/windows/win32/winprog/windows-data-types)

The number of elements in the *Scales*, *InputPixelOffsets*, and *OutputPixelOffsets* arrays. This value must equal the *DimensionCount* provided in the *InputGradientTensor* and *OutputGradientTensor*.

`Scales`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

See [DML_RESAMPLE2_OPERATOR_DESC::Scales](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

`InputPixelOffsets`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

See [DML_RESAMPLE2_OPERATOR_DESC::InputPixelOffsets](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

`OutputPixelOffsets`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

See [DML_RESAMPLE2_OPERATOR_DESC::OutputPixelOffsets](/windows/ai/directml/api/ns-directml-dml_resample2_operator_desc).

## Remarks

This operator is equivalent to **DML_RESAMPLE_GRAD_OPERATOR_DESC** when *InterpolationMode* is set to [DML_INTERPOLATION_MODE_LINEAR](/windows/win32/api/directml/ne-directml-dml_interpolation_mode); or when *InterpolationMode* is set to **DML_INTERPOLATION_MODE_NEAREST_NEIGHBOR**, and *RoundingDirection* to [DML_AXIS_DIRECTION_DECREASING](/windows/win32/api/directml/ne-directml-dml_axis_direction), and *OutputPixelOffsets* are adjusted an additional -0.5.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_5_1**.

## Tensor constraints
*InputGradientTensor* and *OutputGradientTensor* must have the same *DataType* and *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputGradientTensor | Input | 1 to 4 | FLOAT32, FLOAT16 |
| OutputGradientTensor | Output | 1 to 4 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
