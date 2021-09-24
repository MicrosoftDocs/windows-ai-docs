---
UID: NS:directml.DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
title: DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
description: Computes backpropagation gradients for [batch normalization training](ns-directml-dml_batch_normalization_training_operator_desc.md).
helpviewer_keywords: ["DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC","DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC structure","direct3d12.dml_batch_normalization_training_grad_operator_desc","directml/DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC"]
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
 - DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
 - directml/DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
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
 - DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
---

# DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC (directml.h)

Computes backpropagation gradients for [batch normalization training](ns-directml-dml_batch_normalization_training_operator_desc.md).

This operator performs multiple computations, which are detailed in the separate output descriptions.

Any dimension in *MeanTensor*, *VarianceTensor*, and *ScaleTensor* can be set to 1, and be automatically broadcast to match *InputTensor*, but otherwise must equal the corresponding dimension's size from *InputTensor*. 

*OutputScaleGradientTensor* and *OutputBiasGradientTensor* are computed using sums across the set of dimensions for which *MeanTensor*, *ScaleTensor* and *VarianceTensor* sizes equal one.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.7 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax
```cpp
struct DML_BATCH_NORMALIZATION_TRAINING_GRAD_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* InputGradientTensor;
    const DML_TENSOR_DESC* MeanTensor;
    const DML_TENSOR_DESC* VarianceTensor;
    const DML_TENSOR_DESC* ScaleTensor;

    const DML_TENSOR_DESC* OutputGradientTensor;
    const DML_TENSOR_DESC* OutputScaleGradientTensor;
    const DML_TENSOR_DESC* OutputBiasGradientTensor;

    FLOAT Epsilon;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the input data. This is typically the same tensor that was provided as the *InputTensor* to [**DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC**](ns-directml-dml_batch_normalization_training_operator_desc.md) in the forward pass.

`InputGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The incoming gradient tensor. This is typically obtained from the output of backpropagation of a preceding layer.  

`MeanTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the mean data. This is typically the same tensor that was returned by *MeanTensor* from [**DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC**](ns-directml-dml_batch_normalization_training_operator_desc.md) in the forward pass.

`VarianceTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the variance data. This is typically the same tensor that was returned as the *OutputVarianceTensor* from [**DML_BATCH_NORMALIZATION_TRAINING_OPERATOR_DESC**](ns-directml-dml_batch_normalization_training_operator_desc.md) in the forward pass. 

`ScaleTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

A tensor containing the scale data.

`OutputGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

For every corresponding value in the inputs:  

```
Coef0 = 1.0f / sqrt(Variance + Epsilon)
Coef1 = InputGradient * (Input - mean(Input))
InputGradientCentered = InputGradient - mean(InputGradient)
InputCentered = InputCentered - mean(InputCentered)
OutputGradient = Scale * Coef0 * (InputGradientCentered - InputCentered * mean(Coef1) / (Variance + Epsilon))
```

`OutputScaleGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The following computation is done or every corresponding value in the inputs: `OutputScaleGradient = sum(InputGradient * (Input - Mean) / sqrt(Variance + Epsilon))`

`OutputBiasGradientTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The following computation is done or every corresponding value in the inputs: `OutputBiasGradient = sum(InputGradient)`  

`Epsilon`

Type: [**FLOAT**](/windows/desktop/winprog/windows-data-types)

A small float value added to the variance to avoid zero.

## Availability
This operator was introduced in `DML_FEATURE_LEVEL_4_1`.

## Tensor constraints
* *InputGradientTensor*, *InputTensor*, *MeanTensor*, *OutputBiasGradientTensor*, *OutputGradientTensor*, *OutputScaleGradientTensor*, *ScaleTensor*, and *VarianceTensor* must have the same *DataType* and *DimensionCount*.
* *MeanTensor*, *OutputBiasGradientTensor*, *OutputScaleGradientTensor*, *ScaleTensor*, and *VarianceTensor* must have the same *Sizes*.
* *InputGradientTensor*, *InputTensor*, and *OutputGradientTensor* must have the same *Sizes*.

## Tensor support
### DML_FEATURE_LEVEL_4_1 and above
| Tensor | Kind | Dimensions | Supported dimension counts | Supported data types |
| ------ | ---- | ---------- | -------------------------- | -------------------- |
| InputTensor | Input | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| InputGradientTensor | Input | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| MeanTensor | Input | { MeanDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| VarianceTensor | Input | { MeanDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| ScaleTensor | Input | { MeanDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputGradientTensor | Output | { InputDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputScaleGradientTensor | Output | { MeanDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |
| OutputBiasGradientTensor | Output | { MeanDimensions[] } | 1 to 8 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
