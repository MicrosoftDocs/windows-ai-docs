---
author: eliotcowley
title: MLOperatorAttribute struct
description: Specifies the name and properties of an attribute of a custom operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorAttribute
ms.localizationpriority: medium
---

# MLOperatorAttribute struct

Specifies the name and properties of an attribute of a custom operator. This is used when registering custom operator kernels and custom operator schema.

## Fields

| Name     | Type                    | Description |
|----------|-------------------------|-------------|
| name     | char*                   | NULL-terminated UTF-8 string representing the name of the attribute in the associated operator type. |
| required | bool                    | Whether the attribute is required in any model using the associated operator type. |
| type     | MLOperatorAttributeType | The type of the attribute in the associated operator type. |

[!INCLUDE [help](../includes/get-help.md)]