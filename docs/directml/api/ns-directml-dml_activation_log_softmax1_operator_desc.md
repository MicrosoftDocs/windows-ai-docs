---
UID: NS:directml.DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC
title: DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC structure
description: Performs a natural log-of-softmax activation function on each element of *InputTensor*, placing the result into the corresponding element of *OutputTensor*.
helpviewer_keywords: ["DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC","DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC structure","direct3d12.dml_activation_log_softmax1_operator_desc","directml/DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC"]
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
 - DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC
 - directml/DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC
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
 - DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC
---

# DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC structure (directml.h)

Performs a natural log-of-softmax activation function on each element of *InputTensor*, placing the result into the corresponding element of *OutputTensor*.

```
For 1-D InputTensor:
// Let x[i] to be the current element in the InputTensor, and j be the total number of elements in the InputTensor
f(x[i]) = ln(exp(x[i]) / sum(exp(x[0]), ..., exp(x[j-1])))
```

Where exp(x) is the natural exponentiation function, and ln(x) is the natural logarithm.

> [!IMPORTANT]
> This API is available as part of the DirectML standalone redistributable package (see [Microsoft.AI.DirectML](https://www.nuget.org/packages/Microsoft.AI.DirectML/) version 1.9 and later. Also see [DirectML version history](../dml-version-history.md).

## Syntax

```cpp
struct DML_ACTIVATION_LOG_SOFTMAX1_OPERATOR_DESC
{
    const DML_TENSOR_DESC* InputTensor;
    const DML_TENSOR_DESC* OutputTensor;
    UINT AxisCount;
    _Field_size_(AxisCount) const UINT* Axes;
};
```

## Members

`InputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The input tensor to read from.

`OutputTensor`

Type: **const [DML_TENSOR_DESC](/windows/win32/api/directml/ns-directml-dml_tensor_desc)\***

The output tensor to write the results to.

`AxisCount`

Type: [**UINT**](/windows/win32/winprog/windows-data-types)

The number of axes to calculate reduce sum. This field determines the size of the *Axes* array.

`Axes`

Type: \_Field\_size\_\(AxisCount\) **const [UINT](/windows/win32/winprog/windows-data-types)\***

The axes along which to reduce the sum. Values must be in the range `[0, InputTensor.DimensionCount - 1]`.

## Examples

The following examples all use this same three-dimensional input tensor:

```
InputTensor: (Sizes:{2, 2, 2}, DataType:FLOAT32)
[
    [
        [  12, 0],
        [-101, 11],
    ],
    [
        [  3,  234],
        [  0, -101],
    ]
]
```

### Example 1

```
AxisCount: 1
Axes: {1}
OutputTensor: (Sizes:{2, 2, 2}, DataType:FLOAT32)
[
    [               // max element in {12, -101} is 12 and in {0, 11} is 11
        [1, 0],
        [0, 1],
    ],
    [               // max element in {3, 0} is 3 and in {234, -101} is 234
        [1, 1],
        [0, 0],
    ]
]
```

### Example 2

```
AxisCount: 1
Axes: {0}
OutputTensor: (Sizes:{2, 2, 2}, DataType:FLOAT32)
[
    [               // max element in {12, 3} is 12, in {0, 234} is 234, in {-101, 0} is 0 and in {11, -101} is 11
        [1, 0],
        [0, 1],
    ],
    [
        [0, 1],
        [1, 0],
    ]
]
```

### Example 3

```
AxisCount: 2
Axes: {0, 2}
OutputTensor: (Sizes:{2, 2, 2}, DataType:FLOAT32)
[
    [               // max element in {12, 0, 3, 234} is 234 and in {-101, 11, 0, -101} is 11
        [0, 0],
        [0, 1],
    ],
    [
        [0, 1],
        [0, 0],
    ]
]
```

## Remarks

This operator is equivalent to [DML_ACTIVATION_LOG_SOFTMAX_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_activation_log_softmax_operator_desc) when *AxisCount* == 1, and *Axes* == `{DimensionCount - 1}`.

## Availability
This operator was introduced in **DML_FEATURE_LEVEL_5_1**.

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

## See also

* [DML_ACTIVATION_SOFTMAX1_OPERATOR_DESC](/windows/ai/directml/api/ns-directml-dml_activation_softmax1_operator_desc)
* [DML_ARGMAX_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_argmax_operator_desc)
