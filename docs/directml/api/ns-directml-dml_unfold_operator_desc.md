---
UID: NS:directml.DML_UNFOLD_OPERATOR_DESC
title: DML_UNFOLD_OPERATOR_DESC structure
description: Extracts sliding local blocks from a batched input tensor.
ms.topic: reference
tech.root: directml
ms.date: 08/22/2024
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
 - DML_UNFOLD_OPERATOR_DESC
 - directml/DML_UNFOLD_OPERATOR_DESC
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
 - DML_UNFOLD_OPERATOR_DESC
---

# DML_UNFOLD_OPERATOR_DESC structure (directml.h)

Extracts sliding local blocks from a batched input tensor.

Consider a InputTensor with shape (N,C,*), where * is an arbitrary number of spatial dimensions (<=6). The operation flattens each sliding WindowSize'd block within the InputTensor into a column of an OutputTensor. The OutputTensor is a 3D tensor containing sliding local blocks, e.g., patches of images, of shape (N,C×∏(WindowSizes),BlockCount), where N is batch dimension, C×∏(WindowSizes) is the number of values within a window (a window has ∏(WindowSizes) spatial locations each containing a C-channeled vector), and BlockCount is the total number of blocks. The arguments must satisfy:

BlocksPerDimension[d] = ( ( SpatialSize[d] + StartPadding[d] + EndPadding[d] - Dilations[d] * (WindowSizes[d] - 1) - 1 ) / stride[d] ) + 1

BlockCount = ∏<sub>d</sub> BlocksPerDimension[d]

Where:
* SpatialSize is formed by the spatial dimensions of input (*)
* 0 <= d < DimensionCount

Indexing output at the last dimension (column dimension) gives all values within a certain block.

The StartPadding, EndPadding, Strides, and Dilations arguments specify how the sliding blocks are retrieved.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.15.1 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_UNFOLD_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    UINT DimensionCount;
    _Field_size_(DimensionCount) const UINT* WindowSizes;
    _Field_size_(DimensionCount) const UINT* Strides;
    _Field_size_(DimensionCount) const UINT* Dilations;
    _Field_size_(DimensionCount) const UINT* StartPadding;
    _Field_size_(DimensionCount) const UINT* EndPadding;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The input tensor to read from.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The output tensor to write the results to.

`DimensionCount`

Type: [**UINT**](/windows/desktop/winprog/windows-data-types)

The spatial dimensions of *InputTensor*. *DimensionCount* must be in the range `[1, 6]`.

`WindowSizes`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The size of the sliding window.

`Strides`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The stride of the sliding window (with dimensions *WindowSizes*) in the input spatial dimensions. They are separate from the tensor strides included in **DML_TENSOR_DESC**. Step size of the extracted patches.

`Dilations`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The dilations of the sliding window (with dimensions *WindowSizes*) in the input spatial dimensions, by scaling the space between the kernel points. Dilations of the extracted patch.

`StartPadding`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

An array containing the amount of implicit zero-padding to be applied to the beginning of each spatial dimension of *InputTensor*. Start padding of the source tensor.

`EndPadding`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

An array containing the amount of implicit zero-padding to be applied to the end of each spatial dimension of *InputTensor*. End padding of the source tensor.

## Examples

### Example 1.

1-channel unfold.

```
InputTensor: (Sizes:{1, 1, 5, 5}, DataType:FLOAT32)
[[[[ 0.,  1.,  2.,  3.,  4.],
   [ 5.,  6.,  7.,  8.,  9.],
   [10., 11., 12., 13., 14.],
   [15., 16., 17., 18., 19.],
   [20., 21., 22., 23., 24.]]]]
DimensionCount: 2
WindowSizes: {3, 3}
Strides: {1, 1}
Dilations: {1, 1}
StartPadding: {0, 0}
EndPadding: {0, 0}
OutputTensor: (Sizes:{1, 9, 9}, DataType:FLOAT32)
[[[ 0.,  1.,  2.,  5.,  6.,  7., 10., 11., 12.],
  [ 1.,  2.,  3.,  6.,  7.,  8., 11., 12., 13.],
  [ 2.,  3.,  4.,  7.,  8.,  9., 12., 13., 14.],
  [ 5.,  6.,  7., 10., 11., 12., 15., 16., 17.],
  [ 6.,  7.,  8., 11., 12., 13., 16., 17., 18.],
  [ 7.,  8.,  9., 12., 13., 14., 17., 18., 19.],
  [10., 11., 12., 15., 16., 17., 20., 21., 22.],
  [11., 12., 13., 16., 17., 18., 21., 22., 23.],
  [12., 13., 14., 17., 18., 19., 22., 23., 24.]]]
```

### Example 2.

1-channel, padded fold.

```
InputTensor: (Sizes:{1, 1, 5, 5}, DataType:FLOAT32)
[[[[ 0.,  1.,  2.,  3.,  4.],
   [ 5.,  6.,  7.,  8.,  9.],
   [10., 11., 12., 13., 14.],
   [15., 16., 17., 18., 19.],
   [20., 21., 22., 23., 24.]]]]
DimensionCount: 2
WindowSizes: {3, 3}
Strides: {1, 1}
Dilations: {1, 1}
StartPadding: {1, 0}
EndPadding: {1, 0}
OutputTensor: (Sizes:{1, 9, 15}, DataType:FLOAT32)
[[[ 0.,  0.,  0.,  0.,  1.,  2.,  5.,  6.,  7., 10., 11., 12., 15., 16., 17.],
  [ 0.,  0.,  0.,  1.,  2.,  3.,  6.,  7.,  8., 11., 12., 13., 16., 17., 18.],
  [ 0.,  0.,  0.,  2.,  3.,  4.,  7.,  8.,  9., 12., 13., 14., 17., 18., 19.],
  [ 0.,  1.,  2.,  5.,  6.,  7., 10., 11., 12., 15., 16., 17., 20., 21., 22.],
  [ 1.,  2.,  3.,  6.,  7.,  8., 11., 12., 13., 16., 17., 18., 21., 22., 23.],
  [ 2.,  3.,  4.,  7.,  8.,  9., 12., 13., 14., 17., 18., 19., 22., 23., 24.],
  [ 5.,  6.,  7., 10., 11., 12., 15., 16., 17., 20., 21., 22.,  0.,  0., 0.],
  [ 6.,  7.,  8., 11., 12., 13., 16., 17., 18., 21., 22., 23.,  0.,  0., 0.],
  [ 7.,  8.,  9., 12., 13., 14., 17., 18., 19., 22., 23., 24.,  0.,  0., 0.]]]
```

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_4**.

## Tensor constraints
*InputTensor* and *OutputTensor* must have the same *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 3 to 8 |  |
| OutputTensor | Output | 3 to 8 |  |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
