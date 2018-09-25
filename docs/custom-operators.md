---
author: eliotcowley
title: Custom operators
description: TODO
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators
ms.localizationpriority: medium
---

# Custom operators

The Windows Machine Learning custom operator Win32 APIs are located in **MLOperatorAuthor.h**.

## APIs

The following is a list of the custom operator APIs with their syntax and descriptions.

### MLOperatorAttributeType enum

Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.

#### Fields

| Name        | Value | Description                            |
|-------------|-------|----------------------------------------|
| Undefined   | 0     | Undefined (unused).                    |
| Float       | 2     | 32-bit floating point.                 |
| Int         | 3     | 64-bit integer.                        |
| String      | 4     | String value.                          |
| FloatArray  | 7     | Array of 32-bit floating point values. |
| IntArray    | 8     | Array of 64-bit integer values.        |
| StringArray | 9     | Array of string values.                |

### MLOperatorTensorDataType enum

Specifies the data type of a tensor. Each data type numerically matches the corresponding ONNX type.

#### Fields

| Name      | Value | Description                             |
|-----------|-------|-----------------------------------------|
| Undefined | 0     | Undefined (unused).                     |
| Float     | 1     | IEEE 32-bit floating point.             |
| UInt8     | 2     | 8-bit unsigned integer.                 |
| Int8      | 3     | 8-bit signed integer.                   |
| UInt16    | 4     | 16-bit unsigned integer.                |
| Int16     | 5     | 16-bit signed integer.                  |
| Int32     | 6     | 32-bit signed integer.                  |
| Int64     | 7     | 64-bit signed integer.                  |
| String    | 8     | String (unsupported).                   |
| Bool      | 9     | 8-bit boolean. Values other than zero and one result in undefined behavior. |
| Float16   | 10    | IEEE 16-bit floating point.             |
| Double    | 11    | 64-bit double-precision floating point. |

[!INCLUDE [help](includes/get-help.md)]