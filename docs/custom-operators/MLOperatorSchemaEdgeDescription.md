---
author: eliotcowley
title: MLOperatorSchemaEdgeDescription struct
description: Specifies information about an input or output edge of an operator.
ms.author: elcowle
ms.date: 10/02/2018
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
| edgeDescription | [MLOperatorEdgeDescription](MLOperatorEdgeDescription.md) | A structure describing type support. This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::EdgeDescription**. |
| options | [MLOperatorParameterOptions](MLOperatorParameterOptions.md) | Options of the parameter, including whether it is optional or variadic. |
| reserved | **void*** | |
| typeFormat | [MLOperatorSchemaEdgeTypeFormat](MLOperatorSchemaEdgeTypeFormat.md) | The manner in which the type constraints and type mapping are defined. |
| typeLabel | **char*** | A type label string constructed as in ONNX operator schema. For example, "T". This is used when **typeFormat** is **MLOperatorSchemaEdgeTypeFormat::Label**. |

[!INCLUDE [help](../includes/get-help.md)]