---
author: eliotcowley
title: MLOperatorAttributeNameValue struct
description: Specifies the name and value(s) of an attribute of a custom operator.
ms.author: elcowle
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorAttributeNameValue
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorAttributeNameValue
api_location:
- MLOperatorAuthor.h
---

# MLOperatorAttributeNameValue struct

Specifies the name and value(s) of an attribute of a custom operator. This is used when registering custom operator kernels and custom operator schema.

## Fields

| Name       | Type                    | Description |
|------------|-------------------------|-------------|
| floats     | **const float***            | 32-bit floating point value(s). Used when the type field is **MLOperatorAttributeType::Float** or **MLOperatorAttributeType::FloatArray**. |
| ints       | **const int64_t***          | 64-bit integer value(s). Used when the type field is **MLOperatorAttributeType::Int** or **MLOperatorAttributeType::IntArray**. |
| name       | **const char***             | NULL-terminated UTF-8 string representing the name of the attribute in the associated operator type. |
| reserved   | **const void***             |             |
| strings    | **const char\* const***      | NULL-terminated UTF-8 string value(s). Used when the type field is **MLOperatorAttributeType::String** or **MLOperatorAttributeType::StringArray**. |
| type       | [MLOperatorAttributeType](MLOperatorAttributeType.md) | The type of the attribute in the associated operator type. |
| valueCount | **uint32_t**                | The number of elements in the attribute value. This must be 1, except for attributes which are of array types. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
