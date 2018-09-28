---
author: eliotcowley
title: MLOperatorEdgeDescription struct
description: Specifies the properties of an input or output edge of an operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorEdgeDescription
ms.localizationpriority: medium
---

# MLOperatorEdgeDescription struct

Specifies the properties of an input or output edge of an operator.

## Fields

| Name           | Type                     | Description           |
|----------------|--------------------------|-----------------------|
| edgeType       | MLOperatorEdgeType       | The type of the edge. |
| reserved       | uint64_t                 |                       |
| tensorDataType | MLOperatorTensorDataType | The data type of a tensor. Used when **edgeType** is set to **Tensor**. |

[!INCLUDE [help](../includes/get-help.md)]