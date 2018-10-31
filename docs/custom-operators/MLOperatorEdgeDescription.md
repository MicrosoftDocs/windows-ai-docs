---
author: eliotcowley
title: MLOperatorEdgeDescription struct
description: Specifies the properties of an input or output edge of an operator.
ms.author: elcowle
ms.date: 10/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorEdgeDescription
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- MLOperatorEdgeDescription
api_location:
- MLOperatorAuthor.h
---

# MLOperatorEdgeDescription struct

Specifies the properties of an input or output edge of an operator.

## Fields

| Name           | Type                     | Description           |
|----------------|--------------------------|-----------------------|
| edgeType       | [MLOperatorEdgeType](MLOperatorEdgeType.md)       | The type of the edge. |
| reserved       | **uint64_t**                 |                       |
| tensorDataType | [MLOperatorTensorDataType](MLOperatorTensorDataType.md) | The data type of a tensor. Used when **edgeType** is set to **Tensor**. |

## Requirements

| | |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../includes/get-help.md)]
