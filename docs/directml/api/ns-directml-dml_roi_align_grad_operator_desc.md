---
UID: NS:directml.DML_ROI_ALIGN_GRAD_OPERATOR_DESC
title: DML_ROI_ALIGN_GRAD_OPERATOR_DESC
description: Computes backpropagation gradients for [ROI_ALIGN](/windows/win32/api/directml/ns-directml-dml_roi_align_operator_desc) and [ROI_ALIGN1](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).
helpviewer_keywords: ["DML_ROI_ALIGN_GRAD_OPERATOR_DESC","DML_ROI_ALIGN_GRAD_OPERATOR_DESC structure","direct3d12.dml_roi_align_grad_operator_desc","directml/DML_ROI_ALIGN_GRAD_OPERATOR_DESC"]
ms.topic: reference
tech.root: directml
ms.date: 09/23/2021
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
 - DML_ROI_ALIGN_GRAD_OPERATOR_DESC
 - directml/DML_ROI_ALIGN_GRAD_OPERATOR_DESC
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
 - DML_ROI_ALIGN_GRAD_OPERATOR_DESC
---

# DML_ROI_ALIGN_GRAD_OPERATOR_DESC (directml.h)

Computes backpropagation gradients for [ROI_ALIGN](/windows/win32/api/directml/ns-directml-dml_roi_align_operator_desc) and [ROI_ALIGN1](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

Recall that [DML_ROI_ALIGN1_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc) crops and rescales subregions of an input tensor using either neareast-neighbor sampling or bilinear interpolation. Given an `InputGradientTensor` with the same sizes as the *output* of an equivalent **DML_OPERATOR_ROI_ALIGN1**, this operator produces an `OutputGradientTensor` with the same sizes as the *input* of **DML_OPERATOR_ROI_ALIGN1**.

As an example, consider a **DML_OPERATOR_ROI_ALIGN1** that performs a *nearest-neighbor* scaling of 1.5x in the width, and 0.5x in the height, for 4 non-overlapping crops of an input with dimensions `[1, 1, 4, 4]`:

```
ROITensor
[[0, 0, 2, 2],
 [2, 0, 4, 2],
 [0, 2, 2, 4],
 [2, 2, 4, 4]]

BatchIndicesTensor
[0, 0, 0, 0]

InputTensor
[[[[1,   2, |  3,  4],    RoiAlign1     [[[[ 1,  1,  2]]],
   [5,   6, |  7,  8],       -->         [[[ 3,  3,  4]]],
   ------------------                    [[[ 9,  9, 10]]],
   [9,  10, | 11, 12],                   [[[11, 11, 12]]]]
   [13, 14, | 15, 16]]]]
```

Notice how the 0th element of each region contributes to two elements in the output&mdash;the 1st element contributes to one element in the output, and the 2nd and 3rd elements contribute to no elements of the output.

The corresponding **DML_OPERATOR_ROI_ALIGN_GRAD** would perform the following:

```
InputGradientTensor                  OutputGradientTensor
[[[[ 1,  2,  3]]],    ROIAlignGrad   [[[[ 3,  3, |  9,  6],
 [[[ 4,  5,  6]]],         -->          [ 0,  0, |  0,  0],
 [[[ 7,  8,  9]]],                      ------------------
 [[[10, 11, 12]]]]                      [15,  9, | 21, 12],
                                        [ 0,  0, |  0,  0]]]]
```

In summary, **DML_OPERATOR_ROI_ALIGN_GRAD** behaves similarly to a [DML_OPERATOR_RESAMPLE_GRAD](/windows/win32/api/directml/ns-directml-dml_resample_grad_operator_desc) performed on each batch of the `InputGradientTensor` when regions don't overlap.

For `OutputROIGradientTensor`, the math is a little different, and can be summarized by the following pseudo code (assuming that `MinimumSamplesPerOutput == 1` and `MaximumSamplesPerOutput == 1`):

```
for each region of interest (ROI):
    for each inputGradientCoordinate:
        for each inputCoordinate that contributed to this inputGradient element:
            topYIndex = floor(inputCoordinate.y)
            bottomYIndex = ceil(inputCoordinate.y)
            leftXIndex = floor(inputCoordinate.x)
            rightXIndex = ceil(inputCoordinate.x)

            yLerp = inputCoordinate.y - topYIndex
            xLerp = inputCoordinate.x - leftXIndex

            topLeft = InputTensor[topYIndex][leftXIndex]
            topRight = InputTensor[topYIndex][rightXIndex]
            bottomLeft = InputTensor[bottomYIndex][leftXIndex]
            bottomRight = InputTensor[bottomYIndex][rightXIndex]

            inputGradientWeight = InputGradientTensor[inputGradientCoordinate.y][inputGradientCoordinate.x]
            imageGradY = (1 - xLerp) * (bottomLeft - topLeft) + xLerp * (bottomRight - topRight)
            imageGradX = (1 - yLerp) * (topRight - topLeft) + yLerp * (bottomRight - bottomLeft)

            imageGradY *= inputGradientWeight
            imageGradX *= inputGradientWeight

            OutputROIGradientTensor[roiIndex][0] += imageGradX * (inputWidth - inputGradientCoordinate.x)
            OutputROIGradientTensor[roiIndex][1] += imageGradY * (inputHeight - inputGradientCoordinate.y)
            OutputROIGradientTensor[roiIndex][2] += imageGradX * inputGradientCoordinate.x
            OutputROIGradientTensor[roiIndex][3] += imageGradY * inputGradientCoordinate.y
```

`OutputGradientTensor` or `OutputROIGradientTensor` can be omitted if only one is needed; but at least one must be supplied.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.7 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax
```cpp
struct DML_ROI_ALIGN_GRAD_OPERATOR_DESC
{
    _Maybenull_ const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* InputGradientTensor;
    const DML_TENSOR_DESC* ROITensor;
    const DML_TENSOR_DESC* BatchIndicesTensor;
    _Maybenull_ const DML_TENSOR_DESC* OutputGradientTensor;
    _Maybenull_ const DML_TENSOR_DESC* OutputROIGradientTensor;
    DML_REDUCE_FUNCTION ReductionFunction;
    DML_INTERPOLATION_MODE InterpolationMode;
    FLOAT SpatialScaleX;
    FLOAT SpatialScaleY;
    FLOAT InputPixelOffset;
    FLOAT OutputPixelOffset;
    UINT MinimumSamplesPerOutput;
    UINT MaximumSamplesPerOutput;
    BOOL AlignRegionsToCorners;
};
```

## Members

`InputTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the input data from the forward pass with dimensions `{ BatchCount, ChannelCount, InputHeight, InputWidth }`. This tensor *must* be supplied when `OutputROIGradientTensor` is supplied, or when `ReductionFunction == DML_REDUCE_FUNCTION_MAX`. This is the same tensor that would be supplied to `InputTensor` for **DML_OPERATOR_ROI_ALIGN** or **DML_OPERATOR_ROI_ALIGN1**.

`ROITensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the regions of interest (ROI) data&mdash;a series of bounding boxes in floating point coordinates that point into the X and Y dimensions of the input tensor. The allowed dimensions of `ROITensor` are `{ NumROIs, 4 }`, `{ 1, NumROIs, 4 }`, or `{ 1, 1, NumROIs, 4 }`. For each ROI, the values will be the coordinates of its top-left and bottom-right corners in the order `[x1, y1, x2, y2]`. Regions can be empty, meaning that all output pixels come from the single input coordinate, and regions can be inverted (for example, x2 less than x1), meaning that the output receives a mirrored/flipped version of the input. These coordinates are first scaled by `SpatialScaleX` and `SpatialScaleY`, but if they are both 1.0 then the region rectangles simply correspond directly to the input tensor coordinates. This is the same tensor that would be supplied to `ROITensor` for **DML_OPERATOR_ROI_ALIGN** or **DML_OPERATOR_ROI_ALIGN1**.

`BatchIndicesTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the batch indices to extract the ROIs from. The allowed dimensions of `BatchIndicesTensor` are `{ NumROIs }`, `{ 1, NumROIs }`, `{ 1, 1, NumROIs }`, or `{ 1, 1, 1, NumROIs }`. Each value is the index of a batch from `InputTensor`. The behavior is undefined if the values are not in the range `[0, BatchCount)`. This is the same tensor that would be supplied to `BatchIndicesTensor` for **DML_OPERATOR_ROI_ALIGN** or **DML_OPERATOR_ROI_ALIGN1**.

`OutputGradientTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An output tensor containing the backpropagated gradients with respect to `InputTensor`. Typically this tensor would have the same sizes as the *input* of the corresponding **DML_OPERATOR_ROI_ALIGN1** in the forward pass. If `OutputROIGradientTensor` is not supplied, then `OutputGradientTensor` *must* be supplied.

`OutputROIGradientTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An output tensor containing the backpropagated gradients with respect to `ROITensor`. This tensor needs to have the same sizes as `ROITensor`. If `OutputGradientTensor` is not supplied, then `OutputROIGradientTensor` *must* be supplied.

`ReductionFunction`

Type: [**DML_REDUCE_FUNCTION**](/windows/win32/api/directml/ne-directml-dml_reduce_function)

See [DML_ROI_ALIGN1_OPERATOR_DESC::ReductionFunction](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`InterpolationMode`

Type: [**DML_INTERPOLATION_MODE**](/windows/win32/api/directml/ne-directml-dml_interpolation_mode)

See [DML_ROI_ALIGN1_OPERATOR_DESC::InterpolationMode](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`SpatialScaleX`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::SpatialScaleX](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`SpatialScaleY`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::SpatialScaleY](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`InputPixelOffset`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::InputPixelOffset](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`OutputPixelOffset`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::OutputPixelOffset](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`MinimumSamplesPerOutput`

Type: [**UINT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::MinimumSamplesPerOutput](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`MaximumSamplesPerOutput`

Type: [**UINT**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::MaximumSamplesPerOutput](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

`AlignRegionsToCorners`

Type: [**BOOL**](/windows/desktop/winprog/windows-data-types)

See [DML_ROI_ALIGN1_OPERATOR_DESC::AlignRegionsToCorners](/windows/win32/api/directml/ns-directml-dml_roi_align1_operator_desc).

## Availability
This operator was introduced in `DML_FEATURE_LEVEL_4_1`.

## Tensor constraints
*InputGradientTensor*, *InputTensor*, *OutputGradientTensor*, *OutputROIGradientTensor*, and *ROITensor* must have the same *DataType*.

## Tensor support
### DML_FEATURE_LEVEL_4_1 and above
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Optional input | 4 | FLOAT32, FLOAT16 |
| InputGradientTensor | Input | 4 | FLOAT32, FLOAT16 |
| ROITensor | Input | 2 to 4 | FLOAT32, FLOAT16 |
| BatchIndicesTensor | Input | 1 to 4 | UINT32 |
| OutputGradientTensor | Optional output | 4 | FLOAT32, FLOAT16 |
| OutputROIGradientTensor | Optional output | 2 to 4 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
