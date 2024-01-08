---
UID: NS:directml.DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC
title: DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC structure
description: Performs a hard swish activation function on every element in *InputTensor*, placing the result into the corresponding element of *OutputTensor*.
prerelease: false
ms.topic: reference
tech.root: directml
ms.date: 01/05/2024
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
 - DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC
 - directml/DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC
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
 - DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC
---

# DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC structure (directml.h)

1

Performs a hard swish activation function on every element in *InputTensor*, placing the result into the corresponding element of *OutputTensor*.

```
f(x) = x * HardSigmoid(x, Alpha, Beta)
```

This operator supports in-place execution, meaning that the output tensor is permitted to alias *InputTensor* during binding.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.13 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_ACTIVATION_HARD_SWISH_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    FLOAT Alpha;
    FLOAT Beta;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The input tensor to read from.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The output tensor to write the results to.

`Alpha`

Type: [**FLOAT**](/windows/win32/winprog/windows-data-types)

The alpha coefficient. A typical default for this value is 0.2.

`Beta`

Type: [**FLOAT**](/windows/win32/winprog/windows-data-types)

The beta coefficient.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_6_2**.

## Tensor constraints
*InputTensor* and *OutputTensor* must have the same *DataType*, *DimensionCount*, and *Sizes*.

## Tensor support
| Tensor | Kind | Supported dimension counts | Supported data types |
| ------ | ---- | -------------------------- | -------------------- |
| InputTensor | Input | 1 to 8 | FLOAT32, FLOAT16 |
| OutputTensor | Output | 1 to 8 | FLOAT32, FLOAT16 |

## Requirements
| &nbsp; | &nbsp; |
| ---- |:---- |
| **Header** | directml.h |
