---
author: eliotcowley
title: ILearningModelOperatorProviderNative.GetRegistry method
description: Gets an IMLOperatorRegistry object containing custom operator definitions.
ms.author: elcowle
ms.date: 10/05/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, GetRegistry
ms.localizationpriority: medium
---

# ILearningModelOperatorProviderNative.GetRegistry method

Gets an [IMLOperatorRegistry](../custom-operators/IMLOperatorRegistry.md) object containing custom operator definitions.

```cpp
void GetRegistry(
    IMLOperatorRegistry** ppOperatorRegistry);
```

## Parameters

| Name | Type | Description |
|------|------|-------------|
| ppOperatorRegistry | [IMLOperatorRegistry](../custom-operators/IMLOperatorRegistry.md)** | The **IMLOperatorRegistry** object containing custom operator definitions. |

## Returns

**void**
This method does not return a value.

[!INCLUDE [help](../includes/get-help.md)]