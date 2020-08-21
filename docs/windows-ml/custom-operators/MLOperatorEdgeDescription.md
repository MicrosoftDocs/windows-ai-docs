---
title: MLOperatorEdgeDescription struct
description: Learn about the MLOperatorEdgeDescription struct. This struct specifies the properties of an input or output edge of an operator.
ms.date: 4/1/2019
ms.topic: article
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
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
