---
UID: NS:directml.DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
title: DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
description: Performs a batch normalization on the input.
helpviewer_keywords: ["DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC","DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC structure","direct3d12.dml_batch_normalization_training_operator_desc","directml/DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC"]
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
 - DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
 - directml/DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
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
 - DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
---

# DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC (directml.h)

Performs a batch normalization on the input. This operator performs the following computation: `Output = FusedActivation(Scale * ((Input - Mean) / sqrt(Variance + Epsilon)) + Bias + FusedAdd)`.

Any dimension in *ScaleTensor* and *BiasTensor* can be set to 1, and be automatically broadcast to match *InputTensor*, but otherwise must equal the corresponding dimension's size from *InputTensor*. *MeanTensor* and *VarianceTensor* are computed on the input across the set of dimensions for which *ScaleTensor* and *BiasTensor* sizes equal one.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.7 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax
```cpp
struct DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* ScaleTensor;
    const DML_TENSOR_DESC* BiasTensor;
    _Maybenull_ const DML_TENSOR_DESC* FusedAddTensor;
    const DML_TENSOR_DESC* OutputTensor;
    const DML_TENSOR_DESC* OutputMeanTensor;
    const DML_TENSOR_DESC* OutputVarianceTensor;
    FLOAT Epsilon;
    _Maybenull_ const DML_OPERATOR_DESC* FusedActivation;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the Input data.

`ScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the Scale data. 

`BiasTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the Bias  data. 

`FusedAddTensor`

Type: \_Maybenull\_ **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

An optional tensor containing data that is added to the result prior to FusedActivation, if any.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor to write the results to.

`OutputMeanTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor to write the mean of the input to.

`OutputVarianceTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor to write the variance of the input to.

`Epsilon`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

The epsilon value to use to avoid division by zero.

`FusedActivation`

Type: \_Maybenull\_ **const [DML_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_operator_desc)\***

An optional fused activation layer to apply after the normalization.

## Availability
This operator was introduced in `DML_FEATURE_LEVEL_4_1`.

## Tensor constraints
* *BiasTensor*, *FusedAddTensor*, *InputTensor*, *OutputMeanTensor*, *OutputTensor*, *OutputVarianceTensor*, and *ScaleTensor* must have the same *DataType* and *DimensionCount*.
* *BiasTensor*, *OutputMeanTensor*, *OutputVarianceTensor*, and *ScaleTensor* must have the same *Sizes*.
* *FusedAddTensor*, *InputTensor*, and *OutputTensor* must have the same *Sizes*.

## Tensor support
### DML_FEATURE_LEVEL_4_1 and above
| Tensor | Kind | Dimensions | Supported dimension counts | Supported data types |
| ------ | ---- | ---------- | -------------------------- | -------------------- |
| InputTensor | Input | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| ScaleTensor | Input | { ScaleDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| BiasTensor | Input | { ScaleDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| FusedAddTensor | Optional input | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputTensor | Output | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputMeanTensor | Output | { ScaleDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputVarianceTensor | Output | { ScaleDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
