---
author: eliotcowley
title: MLOperatorParameterOptions enum
description: Specifies option flags of input and output edges of operators.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorParameterOptions
ms.localizationpriority: medium
---

# MLOperatorParameterOptions enum

Specifies option flags of input and output edges of operators. These options are used while defining custom operator schema.

## Fields

| Name | Value | Description |
|------|-------|-------------|
| Single | 0 | There is a single instance of the input or output. |
| Optional | 1 | The input or output may be omitted. |
| Variadic | 2 | The number of instances of the operator is variable. Variadic parameters must be last among the set of inputs or outputs. |

[!INCLUDE [help](../includes/get-help.md)]