---
author: eliotcowley
title: MLOperatorEdgeDescription struct
description: Specifies the properties of an input or output edge of an operator.
ms.author: elcowle
ms.date: 10/02/2018
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
| edgeType       | [MLOperatorEdgeType](MLOperatorEdgeType.md)       | The type of the edge. |
| reserved       | **uint64_t**                 |                       |
| tensorDataType | [MLOperatorTensorDataType](MLOperatorTensorDataType.md) | The data type of a tensor. Used when **edgeType** is set to **Tensor**. |

[!INCLUDE [help](../includes/get-help.md)]