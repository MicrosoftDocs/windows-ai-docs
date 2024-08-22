---
UID: NS:directml.DML_FOLD_OPERATOR_DESC
title: DML_FOLD_OPERATOR_DESC structure
description: Combines an array of patches formed from a sliding window into a large containing tensor.
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
 - DML_FOLD_OPERATOR_DESC
 - directml/DML_FOLD_OPERATOR_DESC
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
 - DML_FOLD_OPERATOR_DESC
---

# DML_FOLD_OPERATOR_DESC structure (directml.h)

Combines an array of patches formed from a sliding window into a large containing tensor.

Consider a batched input tensor containing sliding local blocks, e.g., patches of images, of shape (N,C×∏(WindowSizes),BlockCount), where N is batch dimension, C×∏(WindowSizes) is the number of values within a window (a window has ∏(WindowSizes) spatial locations each containing a C-channeled vector), and BlockCount is the total number of blocks. This operation combines these local blocks into the large output tensor of shape (N,C,OutputSize[0],OutputSize[1],…) by summing the overlapping values. The arguments must satisfy:

BlocksPerDimension[d] = ( ( SpatialSize[d] + StartPadding[d] + EndPadding[d] - Dilations[d] * (WindowSizes[d] - 1) - 1 ) / stride[d] ) + 1

BlockCount = ∏<sub>d</sub> BlocksPerDimension[d]

Where:
* 0 <= d < DimensionCount

The OutputSize (the OutputTensor's size) describes the spatial shape of the large containing tensor of the sliding local blocks. 

The StartPadding, EndPadding, Strides, and Dilations arguments specify how the sliding blocks are retrieved.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.15.1 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_FOLD_OPERATOR_DESC
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

The spatial dimensions of *InputTensor*. *DimensionCount* must be <= 6.

`WindowSizes`

Type: \_Field_size\_(DimensionCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\***

The size of the sliding window. Size of the extracted patch.

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

### Example 1

A 1-channel fold.

```
InputTensor: (Sizes:{1, 9, 4}, DataType:FLOAT32)
[[[ 0.,  1.,  2.,  3.],
  [ 4.,  5.,  6.,  7.],
  [ 8.,  9., 10., 11.],
  [12., 13., 14., 15.],
  [16., 17., 18., 19.],
  [20., 21., 22., 23.],
  [24., 25., 26., 27.],
  [28., 29., 30., 31.],
  [32., 33., 34., 35.]]]
DimensionCount: 2
WindowSizes: {3, 3}
Strides: {1, 1}
Dilations: {1, 1}
StartPadding: {0, 0}
EndPadding: {0, 0}
OutputTensor: (Sizes:{1, 1, 4, 4}, DataType:FLOAT32)
[[[[  0.,   5.,  13.,   9.],
   [ 14.,  38.,  54.,  32.],
   [ 38.,  86., 102.,  56.],
   [ 26.,  57.,  65.,  35.]]]]
```

### Example 2

A 1-channel, padded fold.

```
InputTensor: (Sizes:{1, 9, 8}, DataType:FLOAT32)
[[[ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.],
  [ 8.,  9., 10., 11., 12., 13., 14., 15.],
  [16., 17., 18., 19., 20., 21., 22., 23.],
  [24., 25., 26., 27., 28., 29., 30., 31.],
  [32., 33., 34., 35., 36., 37., 38., 39.],
  [40., 41., 42., 43., 44., 45., 46., 47.],
  [48., 49., 50., 51., 52., 53., 54., 55.],
  [56., 57., 58., 59., 60., 61., 62., 63.],
  [64., 65., 66., 67., 68., 69., 70., 71.]]]
DimensionCount: 2
WindowSizes: {3, 3}
Strides: {1, 1}
Dilations: {1, 1}
StartPadding: {1, 0}
EndPadding: {1, 0}
OutputTensor: (Sizes:{1, 1, 4, 4}, DataType:FLOAT32)
[[[[ 26.,  70., 102.,  60.],
   [ 78., 183., 231., 129.],
   [ 84., 195., 243., 135.],
   [ 82., 182., 214., 116.]]]]
```

### Example 3

A 2-channel, padded fold.

```
InputTensor: (Sizes:{1, 18, 8}, DataType:FLOAT32)
[[[  0.,   1.,   2.,   3.,   4.,   5.,   6.,   7.],
  [  8.,   9.,  10.,  11.,  12.,  13.,  14.,  15.],
  [ 16.,  17.,  18.,  19.,  20.,  21.,  22.,  23.],
  [ 24.,  25.,  26.,  27.,  28.,  29.,  30.,  31.],
  [ 32.,  33.,  34.,  35.,  36.,  37.,  38.,  39.],
  [ 40.,  41.,  42.,  43.,  44.,  45.,  46.,  47.],
  [ 48.,  49.,  50.,  51.,  52.,  53.,  54.,  55.],
  [ 56.,  57.,  58.,  59.,  60.,  61.,  62.,  63.],
  [ 64.,  65.,  66.,  67.,  68.,  69.,  70.,  71.],
  [ 72.,  73.,  74.,  75.,  76.,  77.,  78.,  79.],
  [ 80.,  81.,  82.,  83.,  84.,  85.,  86.,  87.],
  [ 88.,  89.,  90.,  91.,  92.,  93.,  94.,  95.],
  [ 96.,  97.,  98.,  99., 100., 101., 102., 103.],
  [104., 105., 106., 107., 108., 109., 110., 111.],
  [112., 113., 114., 115., 116., 117., 118., 119.],
  [120., 121., 122., 123., 124., 125., 126., 127.],
  [128., 129., 130., 131., 132., 133., 134., 135.],
  [136., 137., 138., 139., 140., 141., 142., 143.]]]
DimensionCount: 2
WindowSizes: {3, 3}
Strides: {1, 1}
Dilations: {1, 1}
StartPadding: {1, 0}
EndPadding: {1, 0}
OutputTensor: (Sizes:{1, 2, 4, 4}, DataType:FLOAT32)
[[[[ 26.,  70., 102.,  60.],
   [ 78., 183., 231., 129.],
   [ 84., 195., 243., 135.],
   [ 82., 182., 214., 116.]],

  [[170., 358., 390., 204.],
   [294., 615., 663., 345.],
   [300., 627., 675., 351.],
   [226., 470., 502., 260.]]]]
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
