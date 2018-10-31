---
author: eliotcowley
title: MLOperatorSchemaEdgeTypeFormat enum
description: Specifies the manner in which types of input and output edges are described.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaEdgeTypeFormat
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorSchemaEdgeTypeFormat
api_location:
- MLOperatorAuthor.h
---

# MLOperatorSchemaEdgeTypeFormat enum

Specifies the manner in which types of input and output edges are described. This is used within [MLOperatorSchemaEdgeDescription](MLOperatorSchemaEdgeDescription.md) while defining custom operator schema.

## Fields

| Name | Value | Description |
|------|-------|-------------|
| EdgeDescription | 0 | The type is defined using [MLOperatorEdgeDescription](MLOperatorEdgeDescription.md). |
| Label | 1 | The type is defined by a type string constructed as in ONNX operator schema. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
