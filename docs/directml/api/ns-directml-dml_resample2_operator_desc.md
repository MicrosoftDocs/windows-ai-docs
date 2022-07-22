---
UID: NS:directml.DML_RESAMPLE2_OPERATOR_DESC
title: DML_RESAMPLE2_OPERATOR_DESC
description: Resamples elements from the source to the destination tensor, using the scale factors to compute the destination tensor size.
helpviewer_keywords: ["DML_RESAMPLE2_OPERATOR_DESC","DML_RESAMPLE2_OPERATOR_DESC structure","direct3d12.dml_resample2_operator_desc","directml/DML_RESAMPLE2_OPERATOR_DESC"]
ms.topic: reference
tech.root: directml
ms.date: 07/21/2022
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
 - DML_RESAMPLE2_OPERATOR_DESC
 - directml/DML_RESAMPLE2_OPERATOR_DESC
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
 - DML_RESAMPLE2_OPERATOR_DESC
---

# DML_RESAMPLE2_OPERATOR_DESC (directml.h)

Resamples elements from the source to the destination tensor, using the scale factors to compute the destination tensor size. You can use a linear or nearest neighbor interpolation mode. The operator supports interpolation across multiple dimensions, not just 2D. So you can keep the same spatial size, but interpolate across channels or across batches. The relationship between the input and output coordinates is the following:

`OutputTensorX = (InputTensorX + InputPixelOffset) * Scale + OutputPixelOffset`

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.9 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax
```cpp
struct DML_RESAMPLE2_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    DML_INTERPOLATION_MODE InterpolationMode;
    DML_AXIS_DIRECTION RoundingDirection;
    UINT DimensionCount;
    _Field_size_(DimensionCount) const FLOAT* Scales;
    _Field_size_(DimensionCount) const FLOAT* InputPixelOffsets;
    _Field_size_(DimensionCount) const FLOAT* OutputPixelOffsets;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The tensor containing the input data.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The tensor to write the output data to.

`InterpolationMode`

Type: [**DML_INTERPOLATION_MODE**](/windows/win32/api/directml/ne-directml-dml_interpolation_mode)

This field determines the kind of interpolation used to choose output pixels.

- **DML_INTERPOLATION_MODE_NEAREST_NEIGHBOR**. Uses the *nearest neighbor* algorithm, which chooses the input element nearest to the corresponding pixel offset for each output element.

- **DML_INTERPOLATION_MODE_LINEAR**. Uses the *linear interpolation* algorithm, which computes the output element by computing the weighted average of the two nearest neighboring input elements per dimension. Resampling is supported up to four dimensions (quadrilinear), where the weighted average is computed on a total of sixteen input elements for each output element.

`RoundingDirection`

Type: [**DML_AXIS_DIRECTION**](/windows/win32/api/directml/ne-directml-dml_axis_direction)

The direction to round along each axis when mapping fractional coordinates back to input pixels. Since bilinear interpolation interpolates fractional coordinates anyway, this affects only **DML_INTERPOLATION_MODE_NEAREST_NEIGHBOR**.

- **DML_AXIS_DIRECTION_INCREASING**. Round fractional coordinates toward increasing axis values (ceil).

- **DML_AXIS_DIRECTION_DECREASING**. Round fractional coordinates toward decreasing axis values (floor).

`DimensionCount`

Type: [**UINT**](/windows/win32/winprog/windows-data-types)

The number of elements in the *Scales*, *InputPixelOffsets*, and *OutputPixelOffsets* arrays. This value must match the dimension count of *InputTensor* and *OutputTensor*.

`Scales`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

The scales to apply when resampling the input, where scales > 1 scale up the image, and scales < 1 scale down the image for that dimension. Note that the scales don't need to be exactly `OutputSize / InputSize`. If the input after scaling is larger than the output bound, then we crop it to the output size. On the other hand, if the input after scaling is smaller than the output bound, then the output edges are clamped.

`InputPixelOffsets`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

The offsets to apply to the input pixels before resampling. When this value is 0, the top left corner of the pixel is used instead of its center, which usually won't give the expected result. To resample the image by using the center of the pixels and to get the same behavior as `DML_RESAMPLE_OPERATOR_DESC`, this value must be 0.5.

`OutputPixelOffsets`

Type: \_Field\_size\_\(DimensionCount\) **const [FLOAT](/windows/win32/winprog/windows-data-types)\***

The offsets to apply to the output pixels after resampling. When this value is 0, the top left corner of the pixel is used instead of its center, which usually won't give the expected result. To resample the image by using the center of the pixels and to get the same behavior as **DML_RESAMPLE_OPERATOR_DESC**, this value must be -0.5.

## Remarks

This operator is equivalent to [DML_RESAMPLE1_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_resample1_operator_desc) when *InterpolationMode* is set to [DML_INTERPOLATION_MODE_LINEAR](/windows/win32/api/directml/ne-directml-dml_interpolation_mode); or when *InterpolationMode* is set to **DML_INTERPOLATION_MODE_NEAREST_NEIGHBOR**, and *RoundingDirection* to [DML_AXIS_DIRECTION_DECREASING](/windows/win32/api/directml/ne-directml-dml_axis_direction), and *OutputPixelOffsets* are adjusted an additional -0.5.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_5_1**.

## Tensor constraints
*InputTensor* and *OutputTensor* must have the same *DataType* and *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 1 to 4 | FLOAT32, FLOAT16 |
| OutputTensor | Output | 1 to 4 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
