---
author: eliotcowley
title: MLCreateOperatorRegistry function
description: Creates an instance of **IMLOperatorRegistry** which may be used to register a custom operator kernel and custom operator schema.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, MLCreateOperatorRegistry
ms.localizationpriority: medium
---

# MLCreateOperatorRegistry function

Creates an instance of [IMLOperatorRegistry](IMLOperatorRegistry.md) which may be used to register a custom operator kernel and custom operator schema.

```cpp
HRESULT MLCreateOperatorRegistry(
    _COM_Outptr_ IMLOperatorRegistry** registry)
```

[!INCLUDE [help](../includes/get-help.md)]