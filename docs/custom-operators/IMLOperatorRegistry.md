---
author: eliotcowley
title: IMLOperatorRegistry interface
description: Represents an instance of a registry for the custom operator kernel and schema.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorRegistry
ms.localizationpriority: medium
---

# IMLOperatorRegistry interface

Represents an instance of a registry for the custom operator kernel and schema. Custom operators may be used with Windows.AI.MachineLearning APIs by returning instances of **IMLOperatorRegistry** through **ILearningModelOperatorProviderNative**.

## Methods

| Name | Description |
|------|-------------|
| [RegisterOperatorKernel](IMLOperatorRegistry_RegisterOperatorKernel.md) | Registers a custom operator kernel. |
| [RegisterOperatorSetSchema](IMLOperatorRegistry_RegisterOperatorSetSchema.md) | Registers a set of custom operator schema comprising an operator set. |

[!INCLUDE [help](../includes/get-help.md)]