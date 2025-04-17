---
UID: NS:directml.DML_QUANTIZE_OPERATOR_DESC
title: DML_QUANTIZE_OPERATOR_DESC structure
description: TBD.
helpviewer_keywords: ["DML_QUANTIZE_OPERATOR_DESC","DML_QUANTIZE_OPERATOR_DESC structure","direct3d12.dml_quantize_operator_desc","directml/DML_QUANTIZE_OPERATOR_DESC"]
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
 - DML_QUANTIZE_OPERATOR_DESC
 - directml/DML_QUANTIZE_OPERATOR_DESC
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
 - DML_QUANTIZE_OPERATOR_DESC
---

# DML_QUANTIZE_OPERATOR_DESC structure (directml.h)

TBD

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.15.0 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_QUANTIZE_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    DML_QUANTIZATION_TYPE QuantizationType;
    UINT QuantizationTensorCount;
    _Field_size_(QuantizationTensorCount) const DML_TENSOR_DESC* QuantizationTensors;
    const DML_TENSOR_DESC* OutputTensor;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The input tensor to read from.

`QuantizationType`

Type: **DML_QUANTIZATION_TYPE**

TBD

`QuantizationTensorCount`

Type: [**UINT**](/windows/win32/winprog/windows-data-types)

TBD. This field determines the size of the *QuantizationTensors* array.

`QuantizationTensors`

Type: \_Field\_size\_\(QuantizationTensorCount\) **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

TBD

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The output tensor to write the results to.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_3**.

## Tensor constraints
*InputTensor*, *OutputTensor*, and *QuantizationTensors* must have the same *DimensionCount*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 1 to 8 | FLOAT32, FLOAT16 |
| QuantizationTensors | Array of inputs | 1 to 8 | FLOAT32, FLOAT16, INT8, INT4, UINT8, UINT4 |
| OutputTensor | Output | 1 to 8 | INT8, INT4, UINT8, UINT4 |
