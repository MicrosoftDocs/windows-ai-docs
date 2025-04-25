---
title: Opt-out of Recall snapshot capture
description: Learn how to out out of Recall snapshot capture for your app.
ms.author: aleader
author: aleader
ms.date: 04/25/2025
ms.topic: article
no-loc: [Recall]
---

# Opt-out of Recall snapshot capture

If your app needs to temporarily opt-out of capture, like when the user is in an InPrivate browsing mode within your app, you can TODO

TODO for app types...

* C# Win32
* C++ Win32
* UWP
* PWA
* Websites in Chrome/Edge?

## [C#](#tab/csharp)

### Install CsWin32 NuGet package

If you haven't yet, install the CsWin32 NuGet package. It's needed for 

```
calling SetInputScope and setting IS_PRIVATE on their HWND
```

---