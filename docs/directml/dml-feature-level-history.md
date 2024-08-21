﻿---
title: DirectML feature level history
description: For general DirectML version history, see [DirectML version history](dml-version-history.md).
ms.topic: article
ms.date: 01/08/2024
author: stevewhims
ms.author: stwhi
---

# DirectML feature level history

For general DirectML version history, see [DirectML version history](dml-version-history.md).


## DML_FEATURE_LEVEL_6_4
Introduced in DirectML version 1.15.0

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_RESAMPLE3**
* **DML_OPERATOR_FOLD**
* **DML_OPERATOR_UNFOLD**

Extended following operators to accept DML_PADDING_MODE_WRAP padding mode.   

* **DML_OPERATOR_PADDING**
* **DML_OPERATOR_PADDING1**

Updated **DML_OPERATOR_ACTIVATION_SOFTPLUS** to allow Steepness <1 

## DML_FEATURE_LEVEL_6_3
Introduced in DirectML version 1.15.0

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_MEAN_VARIANCE_NORMALIZATION2**
* **DML_OPERATOR_MULTIHEAD_ATTENTION1**
* **DML_OPERATOR_QUANTIZE**
* **DML_OPERATOR_DEQUANTIZE**

Introduced **DML_TENSOR_DATA_TYPE_UINT4** and **DML_TENSOR_DATA_TYPE_INT4** data types and currently supported by following operators.
* **DML_OPERATOR_QUANTIZE**
* **DML_OPERATOR_DEQUANTIZE**


## DML_FEATURE_LEVEL_6_2

Introduced in DirectML version 1.13.0.

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ACTIVATION_HARD_SWISH**
* **DML_OPERATOR_ACTIVATION_SWISH**
* **DML_OPERATOR_AVERAGE_POOLING1**
* **DML_OPERATOR_LP_POOLING1**
* **DML_OPERATOR_MATRIX_MULTIPLY_INTEGER_TO_FLOAT**
* **DML_OPERATOR_QUANTIZED_LINEAR_AVERAGE_POOLING**

Extended data type support for the following operators, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For details on the specific support added in [**DML_FEATURE_LEVEL_6_2**](/windows/win32/api/directml/ne-directml-dml_feature_level), see each operator's structure topic.

* **DML_OPERATOR_RESAMPLE2**

Made *ZeroPointTensor* optional for the following operators:

* **DML_OPERATOR_ELEMENT_WISE_DEQUANTIZE_LINEAR**
* **DML_OPERATOR_ELEMENT_WISE_QUANTIZE_LINEAR**

Added a new graph node type **DML_GRAPH_NODE_TYPE_CONSTANT** to enable compile-time optimizations that require content of small tensors.

## DML_FEATURE_LEVEL_6_1

Introduced in DirectML version 1.12.0.

The operator types mentioned below are documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* Added **DML_OPERATOR_MULTIHEAD_ATTENTION**.
* **DML_OPERATOR_GEMM**. *FusedActivation* now supports **DML_OPERATOR_ACTIVATION_SOFTMAX** and **DML_OPERATOR_ACTIVATION_SOFTMAX1**.

## DML_FEATURE_LEVEL_6_0

Introduced in DirectML version 1.11.0.

The operator types mentioned below are documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* Added UINT64 and INT64 data type support for **DML_OPERATOR_ELEMENT_WISE_DIVIDE**, **DML_OPERATOR_ELEMENT_WISE_MODULUS_FLOOR**, and **DML_OPERATOR_ELEMENT_WISE_MODULUS_TRUNCATE**.
* Added FLOAT16 data type support in *ScaleTensor* for **DML_OPERATOR_ELEMENT_WISE_QUANTIZE_LINEAR**.
* Added FLOAT16 data type support in *ScaleTensor* and *OutputTensor* for **DML_OPERATOR_ELEMENT_WISE_DEQUANTIZE_LINEAR**.
* Added **DML_OPERATOR_ELEMENT_WISE_CLIP** operator to the supported fused activation list.

## DML_FEATURE_LEVEL_5_2

Introduced in DirectML version 1.10.0.

The operator types mentioned below are documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

The range of tensor dimension has been increased to 1 to 4 for the following parameters:

* **DML_OPERATOR_MATRIX_MULTIPLY_INTEGER**, *BZeroPointTensor* parameter.
* **DML_OPERATOR_QUANTIZED_LINEAR_CONVOLUTION**, *FilterScaleTensor* parameter.

*ScaleTensor* and *BiasTensor* can be null independent of each other for the following operators:

* **DML_OPERATOR_MEAN_VARIANCE_NORMALIZATION**
* **DML_OPERATOR_MEAN_VARIANCE_NORMALIZATION1** 

## DML_FEATURE_LEVEL_5_1

Introduced in DirectML version 1.9.0.

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ACTIVATION_GELU**
* **DML_OPERATOR_ACTIVATION_SOFTMAX1**
* **DML_OPERATOR_ACTIVATION_LOG_SOFTMAX1**
* **DML_OPERATOR_ACTIVATION_HARDMAX1**
* **DML_OPERATOR_RESAMPLE2**
* **DML_OPERATOR_RESAMPLE_GRAD1**
* **DML_OPERATOR_DIAGONAL_MATRIX1**

Extended data type support for the following operators, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For details on the specific support added in [**DML_FEATURE_LEVEL_5_1**](/windows/win32/api/directml/ne-directml-dml_feature_level), see each operator's structure topic.

* **DML_OPERATOR_ACTIVATION_RELU**
* **DML_OPERATOR_ACTIVATION_RELU_GRAD**
* **DML_OPERATOR_ACTIVATION_PARAMETERIZED_RELU**
* **DML_OPERATOR_ELEMENT_WISE_ADD**
* **DML_OPERATOR_ELEMENT_WISE_DIVIDE**
* **DML_OPERATOR_ELEMENT_WISE_MULTIPLY**
* **DML_OPERATOR_ELEMENT_WISE_SUBTRACT**
* **DML_OPERATOR_DIAGONAL_MATRIX**

## DML_FEATURE_LEVEL_5_0

Introduced in DirectML version 1.8.0.

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_CLIP1**
* **DML_OPERATOR_ELEMENT_WISE_CLIP_GRAD1**
* **DML_OPERATOR_ELEMENT_WISE_NEGATE**
* **DML_OPERATOR_PADDING1**

Extended data type support for the following operators, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For details on the specific support added in [**DML_FEATURE_LEVEL_5_0**](/windows/win32/api/directml/ne-directml-dml_feature_level), see each operator's structure topic.

* **DML_OPERATOR_CUMULATIVE_PRODUCT**
* **DML_OPERATOR_CUMULATIVE_SUMMATION**
* **DML_OPERATOR_DEPTH_TO_SPACE**
* **DML_OPERATOR_DEPTH_TO_SPACE1**
* **DML_OPERATOR_ELEMENT_WISE_CLIP**
* **DML_OPERATOR_ELEMENT_WISE_CLIP_GRAD**
* **DML_OPERATOR_ELEMENT_WISE_CLIP_GRAD1**
* **DML_OPERATOR_ELEMENT_WISE_CLIP1**
* **DML_OPERATOR_ELEMENT_WISE_IF**
* **DML_OPERATOR_ELEMENT_WISE_MAX**
* **DML_OPERATOR_ELEMENT_WISE_MIN**
* **DML_OPERATOR_ELEMENT_WISE_NEGATE**
* **DML_OPERATOR_FILL_VALUE_SEQUENCE**
* **DML_OPERATOR_MAX_POOLING**
* **DML_OPERATOR_MAX_POOLING1**
* **DML_OPERATOR_MAX_POOLING2**
* **DML_OPERATOR_MAX_UNPOOLING**
* **DML_OPERATOR_PADDING**
* **DML_OPERATOR_PADDING1**
* **DML_OPERATOR_REDUCE**, when using one of the following reduce functions.
  * **DML_REDUCE_FUNCTION_L1**
  * **DML_REDUCE_FUNCTION_MAX**
  * **DML_REDUCE_FUNCTION_MIN**
  * **DML_REDUCE_FUNCTION_MULTIPLY**
  * **DML_REDUCE_FUNCTION_SUM**
  * **DML_REDUCE_FUNCTION_SUM_SQUARE**
* **DML_OPERATOR_REVERSE_SUBSEQUENCES**
* **DML_OPERATOR_ROI_ALIGN**
* **DML_OPERATOR_ROI_ALIGN1**
* **DML_OPERATOR_SPACE_TO_DEPTH**
* **DML_OPERATOR_SPACE_TO_DEPTH1**
* **DML_OPERATOR_TOP_K**
* **DML_OPERATOR_TOP_K1**

## DML_FEATURE_LEVEL_4_1

Introduced in DirectML version 1.7.0.

Added the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ROI_ALIGN_GRAD**
* **DML_OPERATOR_BATCH_NORMALIZATION_TRAINING**
* **DML_OPERATOR_BATCH_NORMALIZATION_TRAINING_GRAD**

Extended data type support for the following operators, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For details on the specific support added in [**DML_FEATURE_LEVEL_4_1**](/windows/win32/api/directml/ne-directml-dml_feature_level), see each operator's structure topic.

* **DML_OPERATOR_ELEMENT_WISE_IDENTITY**
* **DML_OPERATOR_ELEMENT_WISE_ADD**
* **DML_OPERATOR_ELEMENT_WISE_SUBTRACT**
* **DML_OPERATOR_ELEMENT_WISE_MULTIPLY**
* **DML_OPERATOR_ELEMENT_WISE_ABS**
* **DML_OPERATOR_ELEMENT_WISE_SIGN**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_EQUALS**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_GREATER_THAN**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_LESS_THAN**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_GREATER_THAN_OR_EQUAL**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_LESS_THAN_OR_EQUAL**
* **DML_OPERATOR_ELEMENT_WISE_BIT_SHIFT_LEFT**
* **DML_OPERATOR_ELEMENT_WISE_BIT_SHIFT_RIGHT**
* **DML_OPERATOR_ELEMENT_WISE_BIT_AND**
* **DML_OPERATOR_ELEMENT_WISE_BIT_OR**
* **DML_OPERATOR_ELEMENT_WISE_BIT_NOT**
* **DML_OPERATOR_ELEMENT_WISE_BIT_XOR**
* **DML_OPERATOR_ELEMENT_WISE_BIT_COUNT**
* **DML_OPERATOR_ARGMIN**
* **DML_OPERATOR_ARGMAX**
* **DML_OPERATOR_CAST**
* **DML_OPERATOR_SLICE**
* **DML_OPERATOR_SLICE1**
* **DML_OPERATOR_SLICE_GRAD**
* **DML_OPERATOR_SPLIT**
* **DML_OPERATOR_JOIN**
* **DML_OPERATOR_GATHER**
* **DML_OPERATOR_GATHER_ELEMENTS**
* **DML_OPERATOR_GATHER_ND**
* **DML_OPERATOR_GATHER_ND1**
* **DML_OPERATOR_SCATTER**
* **DML_OPERATOR_SCATTER_ND**
* **DML_OPERATOR_FILL_VALUE_CONSTANT**
* **DML_OPERATOR_TILE**
* **DML_OPERATOR_ONE_HOT**

## DML_FEATURE_LEVEL_4_0

Introduced in DirectML version 1.6.0.

Added support for the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_QUANTIZED_LINEAR_ADD**
* **DML_OPERATOR_DYNAMIC_QUANTIZE_LINEAR**
* **DML_OPERATOR_ROI_ALIGN1**

Extended data type and dimension count support for the following operators, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For details on the specific support added in [**DML_FEATURE_LEVEL_4_0**](/windows/win32/api/directml/ne-directml-dml_feature_level), see each operator's structure topic.

* **DML_OPERATOR_ACTIVATION_RELU_GRAD**
* **DML_OPERATOR_ADAM_OPTIMIZER**
* **DML_OPERATOR_CONVOLUTION**
* **DML_OPERATOR_CONVOLUTION_INTEGER**
* **DML_OPERATOR_CUMULATIVE_PRODUCT**
* **DML_OPERATOR_CUMULATIVE_SUMMATION**
* **DML_OPERATOR_DIAGONAL_MATRIX**
* **DML_OPERATOR_FILL_VALUE_CONSTANT**
* **DML_OPERATOR_FILL_VALUE_SEQUENCE**
* **DML_OPERATOR_GEMM**
* **DML_OPERATOR_MATRIX_MULTIPLY_INTEGER**
* **DML_OPERATOR_MAX_POOLING_GRAD**
* **DML_OPERATOR_NONZERO_COORDINATES**
* **DML_OPERATOR_QUANTIZED_LINEAR_CONVOLUTION**
* **DML_OPERATOR_QUANTIZED_LINEAR_MATRIX_MULTIPLY**
* **DML_OPERATOR_RANDOM_GENERATOR**
* **DML_OPERATOR_REVERSE_SUBSEQUENCES**

## DML_FEATURE_LEVEL_3_1

Introduced in DirectML version 1.5.0.

Added support for the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_ATAN_YX**
* **DML_OPERATOR_ELEMENT_WISE_CLIP_GRAD**
* **DML_OPERATOR_ELEMENT_WISE_DIFFERENCE_SQUARE**
* **DML_OPERATOR_LOCAL_RESPONSE_NORMALIZATION_GRAD**
* **DML_OPERATOR_CUMULATIVE_PRODUCT**
* **DML_OPERATOR_BATCH_NORMALIZATION_GRAD**

The maximum number of supported dimensions for the following operators has increased from 4 to 8.

* **DML_OPERATOR_BATCH_NORMALIZATION**
* **DML_OPERATOR_CAST**
* **DML_OPERATOR_JOIN**
* **DML_OPERATOR_LP_NORMALIZATION**
* **DML_OPERATOR_MEAN_VARIANCE_NORMALIZATION1**
* **DML_OPERATOR_PADDING**
* **DML_OPERATOR_ACTIVATION_RELU_GRAD**
* **DML_OPERATOR_SLICE_GRAD**
* **DML_OPERATOR_TILE**
* **DML_OPERATOR_TOP_K**
* **DML_OPERATOR_TOP_K1**

## DML_FEATURE_LEVEL_3_0

Introduced in DirectML version 1.4.0.

Added support for the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_BIT_AND**
* **DML_OPERATOR_ELEMENT_WISE_BIT_OR**
* **DML_OPERATOR_ELEMENT_WISE_BIT_XOR**
* **DML_OPERATOR_ELEMENT_WISE_BIT_NOT**
* **DML_OPERATOR_ELEMENT_WISE_BIT_COUNT**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_GREATER_THAN_OR_EQUAL**
* **DML_OPERATOR_ELEMENT_WISE_LOGICAL_LESS_THAN_OR_EQUAL**
* **DML_OPERATOR_ACTIVATION_CELU**
* **DML_OPERATOR_ACTIVATION_RELU_GRAD**
* **DML_OPERATOR_AVERAGE_POOLING_GRAD**
* **DML_OPERATOR_MAX_POOLING_GRAD**
* **DML_OPERATOR_RANDOM_GENERATOR**
* **DML_OPERATOR_NONZERO_COORDINATES**
* **DML_OPERATOR_RESAMPLE_GRAD**
* **DML_OPERATOR_SLICE_GRAD**
* **DML_OPERATOR_ADAM_OPTIMIZER**
* **DML_OPERATOR_ARGMIN**
* **DML_OPERATOR_ARGMAX**
* **DML_OPERATOR_ROI_ALIGN**
* **DML_OPERATOR_GATHER_ND1**

Added the following enhancements.

* The maximum number of tensor dimensions has been increased from 5 to 8. See [DML_TENSOR_DIMENSION_COUNT_MAX1](directml-constants.md).
* Additional support for integer datatypes has been added to the following operators.
  * **DML_OPERATOR_ELEMENT_WISE_POW**
  * **DML_OPERATOR_ELEMENT_WISE_CONSTANT_POW**
  * **DML_OPERATOR_MAX_POOLING**, **DML_OPERATOR_MAX_POOLING1**, and **DML_OPERATOR_MAX_POOLING2**
  * **DML_OPERATOR_REDUCE**, when using **DML_REDUCE_FUNCTION_ARGMIN** or **DML_REDUCE_FUNCTION_ARGMAX**
* The following 64-bit data types have been added, and are supported by select operators.
  * **DML_TENSOR_DATA_TYPE_FLOAT64**
  * **DML_TENSOR_DATA_TYPE_UINT64**
  * **DML_TENSOR_DATA_TYPE_INT64**

Deprecated functionality.

* **DML_REDUCE_FUNCTION_ARGMAX** and **DML_REDUCE_FUNCTION_ARGMIN** have been deprecated. You should prefer to use the standalone **DML_OPERATOR_ARGMIN** and **DML_OPERATOR_ARGMAX** operators in their place.

## DML_FEATURE_LEVEL_2_1

Introduced in DirectML version 1.2.0.

Added the following APIs.

* [IDMLDevice1 interface](/windows/win32/api/directml/nn-directml-idmldevice1)
* Operator graph support (see [IDMLDevice1::CompileGraph](/windows/win32/api/directml/nf-directml-idmldevice1-compilegraph)

Added support for the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_BIT_SHIFT_LEFT**
* **DML_OPERATOR_ELEMENT_WISE_BIT_SHIFT_RIGHT**
* **DML_OPERATOR_ELEMENT_WISE_ROUND**
* **DML_OPERATOR_ELEMENT_WISE_IS_INFINITY**
* **DML_OPERATOR_ELEMENT_WISE_MODULUS_TRUNCATE**
* **DML_OPERATOR_ELEMENT_WISE_MODULUS_FLOOR**
* **DML_OPERATOR_FILL_VALUE_CONSTANT**
* **DML_OPERATOR_FILL_VALUE_SEQUENCE**
* **DML_OPERATOR_CUMULATIVE_SUMMATION**
* **DML_OPERATOR_REVERSE_SUBSEQUENCES**
* **DML_OPERATOR_GATHER_ELEMENTS**
* **DML_OPERATOR_GATHER_ND**
* **DML_OPERATOR_SCATTER_ND**
* **DML_OPERATOR_MAX_POOLING2**
* **DML_OPERATOR_SLICE1**
* **DML_OPERATOR_TOP_K1**
* **DML_OPERATOR_DEPTH_TO_SPACE1**
* **DML_OPERATOR_SPACE_TO_DEPTH1**
* **DML_OPERATOR_MEAN_VARIANCE_NORMALIZATION1**
* **DML_OPERATOR_RESAMPLE1**
* **DML_OPERATOR_MATRIX_MULTIPLY_INTEGER**
* **DML_OPERATOR_QUANTIZED_LINEAR_MATRIX_MULTIPLY**
* **DML_OPERATOR_CONVOLUTION_INTEGER**
* **DML_OPERATOR_QUANTIZED_LINEAR_CONVOLUTION**

Added the following enhancements.

* Additional support for integer datatypes has been added to the following operators.
  * **DML_OPERATOR_ELEMENT_WISE_IDENTITY**
  * **DML_OPERATOR_ELEMENT_WISE_ABS**
  * **DML_OPERATOR_ELEMENT_WISE_ADD**
  * **DML_OPERATOR_ELEMENT_WISE_CLIP**
  * **DML_OPERATOR_ELEMENT_WISE_DIVIDE**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_EQUALS**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_GREATER_THAN**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_LESS_THAN**
  * **DML_OPERATOR_ELEMENT_WISE_MAX**
  * **DML_OPERATOR_ELEMENT_WISE_MEAN**
  * **DML_OPERATOR_ELEMENT_WISE_MIN**
  * **DML_OPERATOR_ELEMENT_WISE_MULTIPLY**
  * **DML_OPERATOR_ELEMENT_WISE_SUBTRACT**
  * **DML_OPERATOR_ELEMENT_WISE_THRESHOLD**
  * **DML_OPERATOR_ELEMENT_WISE_QUANTIZE_LINEAR**
  * **DML_OPERATOR_ELEMENT_WISE_DEQUANTIZE_LINEAR**
  * **DML_OPERATOR_ELEMENT_WISE_SIGN**
  * **DML_OPERATOR_ELEMENT_WISE_IF**
  * **DML_OPERATOR_ACTIVATION_SHRINK**
  * **DML_OPERATOR_PADDING**
  * **DML_OPERATOR_GATHER**
  * **DML_OPERATOR_SCATTER**
  * **DML_OPERATOR_DEPTH_TO_SPACE**
  * **DML_OPERATOR_SPACE_TO_DEPTH**
  * **DML_OPERATOR_TILE**
  * **DML_OPERATOR_TOP_K** and **DML_OPERATOR_TOP_K1**
  * **DML_OPERATOR_ONE_HOT**
  * **DML_OPERATOR_REDUCE**, when using one of the following reduce functions.
    * **DML_REDUCE_FUNCTION_ARGMIN**
    * **DML_REDUCE_FUNCTION_ARGMAX**
    * **DML_REDUCE_FUNCTION_MAX**
    * **DML_REDUCE_FUNCTION_MIN**
    * **DML_REDUCE_FUNCTION_MULTIPLY**
    * **DML_REDUCE_FUNCTION_SUM**
* Relaxed tensor shape restrictions for **DML_OPERATOR_GATHER**

## DML_FEATURE_LEVEL_2_0

Introduced in DirectML version 1.1.0.

Added the following APIs.
* [DMLCreateDevice1 function](/windows/win32/api/directml/nf-directml-dmlcreatedevice1)
* [DML_FEATURE_LEVEL enumeration](/windows/win32/api/directml/ne-directml-dml_feature_level)
* Feature level queries (see [DML_FEATURE_QUERY_FEATURE_LEVELS](/windows/win32/api/directml/ns-directml-dml_feature_query_feature_levels))

Added support for the following operator types, documented in [**DML_OPERATOR_TYPE**](/windows/win32/api/directml/ne-directml-dml_operator_type). For each operator type constant, that topic provides a link to the corresponding structure.

* **DML_OPERATOR_ELEMENT_WISE_SIGN**
* **DML_OPERATOR_ELEMENT_WISE_IS_NAN**
* **DML_OPERATOR_ELEMENT_WISE_ERF**
* **DML_OPERATOR_ELEMENT_WISE_SINH**
* **DML_OPERATOR_ELEMENT_WISE_COSH**
* **DML_OPERATOR_ELEMENT_WISE_TANH**
* **DML_OPERATOR_ELEMENT_WISE_ASINH**
* **DML_OPERATOR_ELEMENT_WISE_ACOSH**
* **DML_OPERATOR_ELEMENT_WISE_ATANH**
* **DML_OPERATOR_ELEMENT_WISE_IF**
* **DML_OPERATOR_ELEMENT_WISE_ADD1**
* **DML_OPERATOR_ACTIVATION_SHRINK**
* **DML_OPERATOR_MAX_POOLING1**
* **DML_OPERATOR_MAX_UNPOOLING**
* **DML_OPERATOR_DIAGONAL_MATRIX**
* **DML_OPERATOR_SCATTER_ELEMENTS**
* **DML_OPERATOR_SCATTER**
* **DML_OPERATOR_ONE_HOT**
* **DML_OPERATOR_RESAMPLE**

Added the following enhancements.

* When binding an input resource for dispatch of an [IDMLOperatorInitializer](/windows/win32/api/directml/nn-directml-idmloperatorinitializer), it's now legal to provide a resource with [D3D12_HEAP_TYPE_CUSTOM](/windows/win32/api/d3d12/ne-d3d12-d3d12_heap_type) (in addition to **D3D12_HEAP_TYPE_DEFAULT**), as long as appropriate heap properties are also set. See [Binding in DirectML](dml-binding.md).
* The following logical boolean operators now support **UINT8** output tensors, in addition to the existing support for **UINT32**.
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_AND**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_EQUALS**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_GREATER_THAN**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_LESS_THAN**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_NOT**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_OR**
  * **DML_OPERATOR_ELEMENT_WISE_LOGICAL_XOR**
* 5D activation functions now support the use of strides on their input and output tensors.

## DML_FEATURE_LEVEL_1_0

The feature level in which DirectML was introduced.

## See also

* [Windows AI](../index.yml)
* [DirectML version history](dml-version-history.md)
* [DML_FEATURE_LEVEL enumeration](/windows/win32/api/directml/ne-directml-dml_feature_level)
* [DMLCreateDevice1 function](/windows/win32/api/directml/nf-directml-dmlcreatedevice1)
* [DML_FEATURE_QUERY_FEATURE_LEVELS structure](/windows/win32/api/directml/ns-directml-dml_feature_query_feature_levels)
