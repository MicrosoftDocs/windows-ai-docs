---
UID: NS:directml.DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
title: DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
description: Performs a clamping (or limiting) operation for each element of *InputTensor*, placing the result into the corresponding element of *OutputTensor*.
helpviewer_keywords: ["DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC","DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC structure","direct3d12.dml_element_wise_clip1_operator_desc","directml/DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC"]
ms.topic: reference
tech.root: directml
ms.date: 01/20/2021
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
 - DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
 - directml/DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
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
 - DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
---

# DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC (directml.h)

Performs the following operation for each element of *InputTensor*, placing the result into the corresponding element of *OutputTensor*. This operator clamps (or limits) every element in the input within the closed interval [*Min*, *Max*].

```
f(x) = min(Max, max(Min, x))
```

Where `max(a,b)` returns the larger of the two values, and `min(a,b)` returns the smaller of the two values a,b.

This operator supports in-place execution, meaning that *OutputTensor* is permitted to alias *InputTensor* during binding.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.8 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax
```cpp
struct DML_ELEMENT_WISE_CLIP1_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    _Maybenull_ const DML_SCALE_BIAS* ScaleBias;
    DML_TENSOR_DATA_TYPE MinMaxDataType;
    DML_SCALAR_UNION Min;
    DML_SCALAR_UNION Max;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The input tensor to read from.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The output tensor to write the results to.

`ScaleBias`

Type: \_Maybenull\_ **const [DML_SCALE_BIAS](/windows/win32/api/directml/ns-directml-dml_scale_bias)\***

An optional scale and bias to apply to the input. If present, this has the effect of applying the function `g(x) = x * scale + bias` to each *input* element prior to computing this operator.

`MinMaxDataType`

Type: [**DML_TENSOR_DATA_TYPE**](/windows/win32/api/directml/ne-directml-dml_tensor_data_type)

The data type of the *Min* and *Max* members, which must match *OutputTensor.DataType*.

`Min`

Type: [**DML_SCALAR_UNION**](/windows/win32/api/directml/ns-directml-dml_scalar_union)

The minimum value, below which the operator replaces the value with *Min*. *MinMaxDataType* determines how to interpret the field.

`Max`

Type: [**DML_SCALAR_UNION**](/windows/win32/api/directml/ns-directml-dml_scalar_union)

The maximum value, above which the operator replaces the value with *Max*. *MinMaxDataType* determines how to interpret the field.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_5_0**.

## Tensor constraints
*InputTensor* and *OutputTensor* must have the same *DataType*, *DimensionCount*, and *Sizes*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 1 to 8 | FLOAT32, FLOAT16, INT64, INT32, INT16, INT8, UINT64, UINT32, UINT16, UINT8 |
| OutputTensor | Output | 1 to 8 | FLOAT32, FLOAT16, INT64, INT32, INT16, INT8, UINT64, UINT32, UINT16, UINT8 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
