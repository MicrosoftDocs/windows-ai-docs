---
author: eliotcowley
title: IMLOperatorAttributes interface
description: Represents the values of an operator's attributes, as determined by a model using the operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorAttributes
ms.localizationpriority: medium
---

# IMLOperatorAttributes interface

Represents the values of an operator's attributes, as determined by a model using the operator. This interface is called by implementations of custom operator kernels, and by implementations of shape and type inferrers.

## Methods

| Name | Description |
|------|-------------|
| [GetAttributeElementCount](IMLOperatorAttributes_GetAttributeElementCount.md) | Gets the count of elements in an attribute. This may be used to determine if an attribute exists, and to determine the count of elements within an attribute of an array type. |

[!INCLUDE [help](../includes/get-help.md)]