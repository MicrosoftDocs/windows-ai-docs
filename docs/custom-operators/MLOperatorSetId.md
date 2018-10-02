---
author: eliotcowley
title: MLOperatorSetId struct
description: Specifies the identity of an operator set.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLOperatorSetId
ms.localizationpriority: medium
---

# MLOperatorSetId struct

Specifies the identity of an operator set.

## Fields

| Name | Type | Description |
|------|------|-------------|
| domain | **const char*** | The domain of the operator, for example, "ai.onnx.ml", or an empty string for the ONNX domain. |
| version | **int32_t** | The version of the operator domain. |

[!INCLUDE [help](../includes/get-help.md)]