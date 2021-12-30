---
title: ILearningModelOperatorProviderNative.GetRegistry method
description: Gets an IMLOperatorRegistry object containing custom operator definitions.
ms.date: 4/2/2019
ms.topic: article
keywords: windows 10, windows machine learning, WinML, GetRegistry
topic_type:
- APIRef
api_type:
- NA
api_name:
- ILearningModelOperatorProviderNative.GetRegistry
api_location:
- windows.ai.machinelearning.native.h
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

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
