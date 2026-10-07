---
title: Set up a C++ project for the Windows ML Runtime API
description: Configure a native C++ project to use the experimental Windows ML Runtime API.
ms.date: 09/22/2026
ms.topic: how-to
dev_langs:
- cpp
---

# Set up a C++ project for the Windows ML Runtime API

> [!IMPORTANT]
> The Windows ML Runtime API is currently experimental and **not supported** for use in production environments. Apps trying out this API should not be published to the Microsoft Store.

This topic shows you how to configure a native C++ project to use the Runtime API and create an
`IWinMLRuntime` instance. Continue to [Run a model with the
Windows ML Runtime API](./tutorial.md) for model loading, tensor binding, and
inference.

## Prerequisites

- A Windows version supported by the
  `Microsoft.Windows.AI.MachineLearning` package. See [Windows ML deployment
  requirements](../distributing-your-app.md#requirements).
- Visual Studio 2022 or later with the **Desktop development with C++** workload.
- A native C++ console project.
- The **experimental** version [`2.7.2021-experimental` of Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.7.2021-experimental) NuGet package.
- For the sample code on these pages, C++20 and the
  [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT)
  NuGet package.

> [!NOTE]
> C++20 and C++/WinRT are requirements of these documentation samples, not of
> the Runtime API. The Runtime API is a native COM-style ABI that can also be
> consumed from C or C++ by using raw COM, WRL, WIL, or another COM helper under
> that app's language requirements.

The CPU execution target is the baseline path. GPU and NPU availability depends
on the device, platform capabilities, and execution backend installed for your
app.

## Configure native NuGet restore

Native Visual C++ projects need a NuGet target moniker so package restore selects
the native Windows assets. Add this property group near the top of the `.vcxproj`
file, before the first `Microsoft.Cpp.*` import:

```xml
<PropertyGroup Label="NuGet">
  <RestoreProjectStyle>PackageReference</RestoreProjectStyle>
  <NuGetTargetMoniker>native,Version=v0.0</NuGetTargetMoniker>
  <NuGetTargetPlatformIdentifier>Windows</NuGetTargetPlatformIdentifier>
  <NuGetTargetPlatformVersion>$(WindowsTargetPlatformVersion)</NuGetTargetPlatformVersion>
</PropertyGroup>
```

Install the `Microsoft.Windows.AI.MachineLearning` package by using **Manage
NuGet Packages** or `PackageReference`. To build the samples on these pages,
also install `Microsoft.Windows.CppWinRT` and set **C++ Language Standard** to
**ISO C++20 Standard (`/std:c++20`)**. The Windows ML package supplies the
Runtime headers, import libraries, and self-contained DLLs; C++/WinRT supplies
the `winrt::com_ptr`, `winrt::check_hresult`, and `<winrt/base.h>` conveniences
used only by the sample code.

For CMake and deployment options, see [Self-contained
installation](../distributing-your-app.md#self-contained-installation).

## Initialize the Runtime

Replace `main.cpp` with the following code:

```cpp
// Copyright (C) Microsoft Corporation. All rights reserved.

#include <Windows.h>
#include <winrt/base.h>

#include <WinMLRuntime.h>

#pragma comment(lib, "windowsapp.lib")

int wmain()
{
    winrt::com_ptr<IWinMLRuntime> runtime;
    winrt::check_hresult(WinMLCreateRuntime(
        __uuidof(IWinMLRuntime),
        runtime.put_void()));

    return 0;
}
```

Build for the architecture that matches the package assets, such as
`Release|x64`. A successful build confirms that NuGet restore found the Runtime
headers and import library and copied the required DLLs beside the executable.

## Next steps

- [Run a model with the Windows ML Runtime API](./tutorial.md)
- [Windows ML Runtime API concepts](./concepts.md)
