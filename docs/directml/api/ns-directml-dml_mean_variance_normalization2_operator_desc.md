---
UID: NS:directml.DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC
title: DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC structure
description: TBD.
helpviewer_keywords: ["DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC","DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC structure","direct3d12.dml_mean_variance_normalization2_operator_desc","directml/DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC"]
ms.topic: reference
tech.root: directml
ms.date: 02/14/2025
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
 - DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC
 - directml/DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC
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
 - DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC
---

# DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC structure (directml.h)

TBD

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.15.0 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_MEAN_VARIANCE_NORMALIZATION2_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    _Maybenull_ const DML_TENSOR_DESC* ScaleTensor;
    _Maybenull_ const DML_TENSOR_DESC* BiasTensor;
    const DML_TENSOR_DESC* OutputTensor;
    UINT AxisCount;
    _Field_size_(AxisCount) const UINT* Axes;
    BOOL UseMean;
    BOOL UseVariance;
    FLOAT Epsilon;
    _Maybenull_ const DML_OPERATOR_DESC* FusedActivation;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the Input data. This tensor's dimensions should be `{ BatchCount, ChannelCount, Height, Width }`.

`ScaleTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the Scale data.

If **DML_FEATURE_LEVEL** is less than **DML_FEATURE_LEVEL_4_0**, then this tensor's dimensions should be `{ ScaleBatchCount, ChannelCount, ScaleHeight, ScaleWidth }`. The dimensions ScaleBatchCount, ScaleHeight, and ScaleWidth should either match *InputTensor*, or be set to 1 to automatically broadcast those dimensions across the input.

If **DML_FEATURE_LEVEL** is greater than or equal to **DML_FEATURE_LEVEL_4_0**, then any dimension can be set to 1, and be automatically broadcast to match *InputTensor*.

If **DML_FEATURE_LEVEL** is less than **DML_FEATURE_LEVEL_5_2**, then this tensor is required if *BiasTensor* is present. If **DML_FEATURE_LEVEL** is greater than or equal to **DML_FEATURE_LEVEL_5_2**, then this tensor can be null regardless of the value of *BiasTensor*.

`BiasTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing the Bias data.

If **DML_FEATURE_LEVEL** is less than **DML_FEATURE_LEVEL_4_0**, then this tensor's dimensions should be `{ BiasBatchCount, ChannelCount, BiasHeight, BiasWidth }`. The dimensions BiasBatchCount, BiasHeight, and BiasWidth should either match *InputTensor*, or be set to 1 to automatically broadcast those dimensions across the input.

If **DML_FEATURE_LEVEL** is greater than or equal to **DML_FEATURE_LEVEL_4_0**, then any dimension can be set to 1, and be automatically broadcast to match *InputTensor*.

If **DML_FEATURE_LEVEL** is less than **DML_FEATURE_LEVEL_5_2**, then this tensor is required if *ScaleTensor* is present. If **DML_FEATURE_LEVEL** is greater than or equal to **DML_FEATURE_LEVEL_5_2**, then this tensor can be null regardless of the value of *ScaleTensor*.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor to write the results to. This tensor's dimensions are `{ BatchCount, ChannelCount, Height, Width }`.

`AxisCount`

Type: <b><a href="/windows/desktop/WinProg/windows-data-types">UINT</a></b>

The number of axes. This field determines the size of the *Axes* array.

`Axes`

Type: \_Field\_size\_(AxisCount) **const [UINT](/windows/desktop/WinProg/windows-data-types)\*** 

The axes along which to calculate the Mean and Variance. 

`UseMean`

Type: <b><a href="/windows/desktop/WinProg/windows-data-types">BOOL</a></b>

TBD

`UseVariance`

Type: <b><a href="/windows/desktop/WinProg/windows-data-types">BOOL</a></b>

TBD

`Epsilon`

Type: <b><a href="/windows/desktop/WinProg/windows-data-types">FLOAT</a></b>

The epsilon value to use to avoid division by zero. A value of 0.00001 is recommended as default.

`FusedActivation`

Type: \_Maybenull\_ **const [DML_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_operator_desc)\***

An optional fused activation layer to apply after the normalization.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_3**.

## Tensor constraints
*BiasTensor*, *InputTensor*, *OutputTensor*, and *ScaleTensor* must have the same *DataType* and *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 1 to 8 | FLOAT32, FLOAT16 |
| ScaleTensor | Optional input | 1 to 8 | FLOAT32, FLOAT16 |
| BiasTensor | Optional input | 1 to 8 | FLOAT32, FLOAT16 |
| OutputTensor | Output | 1 to 8 | FLOAT32, FLOAT16 |
