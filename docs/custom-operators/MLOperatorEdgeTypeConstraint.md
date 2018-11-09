---
author: eliotcowley
title: MLOperatorEdgeTypeConstraint struct
description: Specifies constraints upon the types of edges supported in custom operator kernels and schema.
ms.author: elcowle
ms.date: 11/8/2018
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorEdgeTypeConstraint
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorEdgeTypeConstraint
api_location:
- MLOperatorAuthor.h
---

# MLOperatorEdgeTypeConstraint struct

Specifies constraints upon the types of edges supported in custom operator kernels and schema. The provided type label string corresponds to type labels in the ONNX specification for the same operator. For custom schema, it corresponds to type labels specified within [MLOperatorSchemaEdgeDescription](MLOperatorSchemaEdgeDescription.md) when registering the operator's schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| allowedTypeCount | **uint32_t** | |
| allowedTypes | [MLOperatorEdgeDescription](MLOperatorEdgeDescription.md)* | The set of allowed types for the constraint. |
| typeLabel | **char*** | The label of the type for which the constraint is being defined. This is constructed as in ONNX operator schema. For example, "T". |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
