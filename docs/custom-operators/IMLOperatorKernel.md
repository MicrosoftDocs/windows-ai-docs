---
author: eliotcowley
title: IMLOperatorKernel interface
description: Implemented by custom operator kernels.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorKernel
ms.localizationpriority: medium
---

# IMLOperatorKernel interface

Implemented by custom operator kernels. A factory which creates instances of this interface is supplied when registering custom operator kernels using [IMLOperatorRegistry::RegisterOperatorKernel](IMLOperatorRegistry_RegisterOperatorKernel.md).

## Methods

| Name | Description |
|------|-------------|
| [Compute](IMLOperatorKernel_Compute.md) | Computes the outputs of the kernel. |

[!INCLUDE [help](../includes/get-help.md)]