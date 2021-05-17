---
title: NamedDimensionOverride interface
description: Provides the ability to override named input dimensions to concrete values through LearningModelSessionOptions in order to achieve better runtime performance.
ms.date: 10/14/2020
ms.topic: article
keywords: windows 10, windows machine learning, WinML, NamedDimensionOverride
ms.localizationpriority: medium
topic_type:
- APIRef
api_type:
- NA
api_name:
- NamedDimensionOverride
api_location:
- windows.ai.machinelearning.native.h
---

# NamedDimensionOverride interface

Provides the ability to override named input dimensions to concrete values through [LearningModelSessionOptions](/uwp/api/windows.ai.machinelearning.learningmodelsessionoptions) in order to achieve better runtime performance. Using this API can yield performance improvements, as it allows for preallocation of tensors during session creation that would otherwise be allocated during model evaluation.

## Sample Code

```cpp
void SetNamedDimensionOverrides(LearningModel model) {
    // Create LearningModelSessionOptions
    auto options = LearningModelSessionOptions();
 
    // Override a named input dimension to a concrete value
    options->OverrideNamedDimension(L"dimension_name", 256);
 
    // Create session
    LearningModelSession session = nullptr;
    session = LearningModelSession(model, LearningModelDeviceKind::GPU, options);
}

```

## Requirements

| | Requirement |
|-|-|
| **Minimum supported client** | Windows 10, build 17763 |
| **Minimum supported server** | Windows Server 2019 with Desktop Experience |
| **Header** | windows.ai.machinelearning.native.h |

[!INCLUDE [help](../../includes/get-help.md)]
