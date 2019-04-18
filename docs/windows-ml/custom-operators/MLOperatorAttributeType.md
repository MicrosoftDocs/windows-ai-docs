---
author: eliotcowley
title: MLOperatorAttributeType enum
description: Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorAttributeType
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorAttributeType
api_location:
- MLOperatorAuthor.h
---

# MLOperatorAttributeType enum

Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.

## Fields

| Name        | Value | Description                            |
|-------------|-------|----------------------------------------|
| Undefined   | 0     | Undefined (unused).                    |
| Float       | 2     | 32-bit floating point.                 |
| Int         | 3     | 64-bit integer.                        |
| String      | 4     | String value.                          |
| FloatArray  | 7     | Array of 32-bit floating point values. |
| IntArray    | 8     | Array of 64-bit integer values.        |
| StringArray | 9     | Array of string values.                |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
