---
title: IMLOperatorTypeInferrer interface
description: Implemented by type inferrers to infer the types of an operator's output edges.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTypeInferrer
topic_type:
- APIRef
api_type:
- NA
api_name:
- IMLOperatorTypeInferrer
api_location:
- MLOperatorAuthor.h
---

# IMLOperatorTypeInferrer interface

Implemented by type inferrers to infer the types of an operator's output edges. Type inferrers must be provided when registering schema of custom operators if the [MLOperatorSchemaDescription](MLOperatorSchemaDescription.md) structure cannot express how output types are determined&mdash;for example, when an attribute of the operator determines the data type of one of that operator's outputs.

## Methods

| Name | Description |
|------|-------------|
| [InferOutputTypes](IMLOperatorTypeInferrer_InferOutputTypes.md) | Called to infer types of an operator's output edges. |

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | MLOperatorAuthor.h |

[!INCLUDE [help](../../includes/get-help.md)]
