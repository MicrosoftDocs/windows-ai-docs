---
author: eliotcowley
title: IMLOperatorTensorShapeDescription interface
description: Represents the set of input and output tensor shapes of an operator.
ms.author: elcowle
ms.date: 09/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IMLOperatorTensorShapeDescription
ms.localizationpriority: medium
---

# IMLOperatorTensorShapeDescription interface

Represents the set of input and output tensor shapes of an operator. This interface is called by the factory objects registered to create kernels. It is available to these factory objects unless corresponding kernels are registered using the **MLOperatorKernelOptions::AllowDynamicInputShapes** flag.

[!INCLUDE [help](../includes/get-help.md)]