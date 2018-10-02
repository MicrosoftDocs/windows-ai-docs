---
author: eliotcowley
title: MLOperatorAttributeType enum
description: Specifies the type of an attribute. Each attribute type numerically matches the corresponding ONNX type.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorAttributeType
ms.localizationpriority: medium
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

[!INCLUDE [help](../includes/get-help.md)]