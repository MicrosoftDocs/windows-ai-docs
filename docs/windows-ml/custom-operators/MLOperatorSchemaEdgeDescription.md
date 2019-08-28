---
title: MLOperatorSchemaEdgeDescription struct
description: Specifies information about an input or output edge of an operator.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaEdgeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorSchemaEdgeDescription
api_location:
- MLOperatorAuthor.h
---

# MLOperatorSchemaEdgeDescription struct

Specifies information about an input or output edge of an operator. This is used while defining custom operator schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| edgeDescription | [MLOperatorEdgeDescription](MLOperatorEdgeDescription.md) | A structure describing type support. This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::EdgeDescription**. |
| options | [MLOperatorParameterOptions](MLOperatorParameterOptions.md) | Options of the parameter, including whether it is optional or variadic. |
| reserved | **void*** | |
| typeFormat | [MLOperatorSchemaEdgeTypeFormat](MLOperatorSchemaEdgeTypeFormat.md) | The manner in which the type constraints and type mapping are defined. |
| typeLabel | **char*** | A type label string constructed as in ONNX operator schema. For example, "T". This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::Label**. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
