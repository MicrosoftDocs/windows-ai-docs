---
title: MLOperatorTensorDataType enum
description: Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorTensorDataType
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorTensorDataType
api_location:
- MLOperatorAuthor.h
---

# MLOperatorTensorDataType enum

Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type.

## Fields

| Name       | Value | Description                             |
|------------|-------|-----------------------------------------|
| Undefined  | 0     | Undefined (unused).                     |
| Float      | 1     | IEEE 32-bit floating point.             |
| UInt8      | 2     | 8-bit unsigned integer.                 |
| Int8       | 3     | 8-bit signed integer.                   |
| UInt16     | 4     | 16-bit unsigned integer.                |
| Int16      | 5     | 16-bit signed integer.                  |
| Int32      | 6     | 32-bit signed integer.                  |
| Int64      | 7     | 64-bit signed integer.                  |
| String     | 8     | String (unsupported).                   |
| Bool       | 9     | 8-bit boolean. Values other than zero and one result in undefined behavior. |
| Float16    | 10    | IEEE 16-bit floating point.             |
| Double     | 11    | 64-bit double-precision floating point. |
| UInt32     | 12    | 32-bit unsigned integer.                |
| UInt64     | 13    | 64-bit unsigned integer.                |
| Complex64  | 14    | 64-bit complex type (unsupported).      |
| Complex128 | 15    | 128-bit complex type (unsupported).     |

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
