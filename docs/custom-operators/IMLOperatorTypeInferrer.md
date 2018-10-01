---
author: eliotcowley
title: IMLOperatorTypeInferrer interface
description: Implemented by type inferrers to infer the types of an operator's output edges.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTypeInferrer
ms.localizationpriority: medium
---

# IMLOperatorTypeInferrer interface

Implemented by type inferrers to infer the types of an operator's output edges. Type inferrers must be provided when registering schema of custom operators if the **MLOperatorSchemaDescription** structure cannot express how output types are determined&mdash;for example, when an attribute of the operator determines the data type of one of that operator's outputs.

[!INCLUDE [help](../includes/get-help.md)]