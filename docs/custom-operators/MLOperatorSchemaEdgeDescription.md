---
author: eliotcowley
title: MLOperatorSchemaEdgeDescription struct
description: Specifies information about an input or output edge of an operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSchemaEdgeDescription
ms.localizationpriority: medium
---

# MLOperatorSchemaEdgeDescription struct

Specifies information about an input or output edge of an operator. This is used while defining custom operator schema.

## Fields

| Name | Type | Description |
|------|------|-------------|
| options | MLOperatorParameterOptions | Options of the parameter, including whether it is optional or variadic. |
| typeFormat | MLOperatorSchemaEdgeTypeFormat | The manner in which the type constraints and type mapping are defined. |
| reserved | void* | |
| typeLabel | char* | A type label string constructed as in ONNX operator schema. For example, "T". This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::Label**. |
| edgeDescription | MLOperatorEdgeDescription | A structure describing type support. This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::EdgeDescription**. |

[!INCLUDE [help](../includes/get-help.md)]