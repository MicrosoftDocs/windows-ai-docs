---
author: eliotcowley
title: MLOperatorSchemaEdgeTypeFormat enum
description: Specifies the manner in which types of input and output edges are described.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaEdgeTypeFormat
ms.localizationpriority: medium
---

# MLOperatorSchemaEdgeTypeFormat enum

Specifies the manner in which types of input and output edges are described. This is used within **MLOperatorSchemaEdgeDescription** while defining custom operator schema.

## Fields

| Name | Value | Description |
|------|-------|-------------|
| EdgeDescription | 0 | The type is defined using **MLOperatorEdgeDescription**. |
| Label | 1 | The type is defined by a type string constructed as in ONNX operator schema. |

[!INCLUDE [help](../includes/get-help.md)]