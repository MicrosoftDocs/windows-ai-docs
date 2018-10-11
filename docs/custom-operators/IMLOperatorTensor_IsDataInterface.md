---
author: eliotcowley
title: IMLOperatorTensor.IsDataInterface method
description: Whether the contents of the tensor are represented by an interface type, or byte-addressable memory.
ms.author: elcowle
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, windows machine learning, WinML, custom operators, IsDataInterface
ms.localizationpriority: medium
---

# IMLOperatorTensor.IsDataInterface method

Whether the contents of the tensor are represented by an interface type, or byte-addressable memory. This returns true when kernels are registered using [MLOperatorExecutionType::D3D12](MLOperatorExecutionType.md).

```cpp
bool IsDataInterface()
```

[!INCLUDE [help](../includes/get-help.md)]