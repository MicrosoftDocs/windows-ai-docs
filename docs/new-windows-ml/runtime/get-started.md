---
title: Set up a project for the Windows ML Runtime API
description: Configure a native C++, C#, or Python project to use the experimental Windows ML Runtime and Task APIs, and create a Runtime instance.
author: andrewleader
ms.author: aleader
ms.date: 11/18/2026
ms.topic: how-to
dev_langs:
- cpp
- csharp
- python
---

# Set up a project for the Windows ML Runtime API

> [!IMPORTANT]
> The Windows ML Runtime API is currently experimental and **not supported** for use in production environments. Apps trying out this API should not be published to the Microsoft Store.

This topic shows you how to configure a native C++, C#, or Python project to use the Runtime API and create an
`IWinMLRuntime` instance. Continue to [Run a model with the
Windows ML Runtime API](./tutorial.md) for model loading, tensor binding, and
inference.

## Prerequisites

- A Windows version supported by the
  `Microsoft.Windows.AI.MachineLearning` package. See [Windows ML deployment
  requirements](../distributing-your-app.md#requirements).
- The **experimental** version [`2.9.36-experimental` of Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.9.36-experimental) NuGet package. Windows ML 2.9 adds the [CGC backend](./model-compilation-execution.md) and [Tasks that take a model](../tasks/choose-model-path.md).

### [C#](#tab/csharp)

- The .NET 8 SDK or later.
- A C# console project that targets `net8.0-windows10.0.19041.0` or later.
- [CsWin32](https://github.com/microsoft/CsWin32) 0.3.369 or later. Earlier versions generate `IUnknown*` parameters, such as the adapter that `CreateExecutionTargetFromAdapter` takes, as `IUnknown` values instead of pointers.

### [C++](#tab/cpp)

- Visual Studio 2022 or later with the **Desktop development with C++** workload.
- A native C++ console project.
- For the sample code on these pages, C++20 and the
  [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT)
  NuGet package.

> [!NOTE]
> C++20 and C++/WinRT are requirements of these documentation samples, not of
> the Runtime API. The Runtime API is a native COM-style ABI that can also be
> consumed from C or C++ by using raw COM, WRL, WIL, or another COM helper under
> that app's language requirements.

### [Python](#tab/python)

- Python 3.10 or later.
- The `windowsml` wheel, version `2.9.36a0`. Its version matches the `2.9.36-experimental` NuGet package.

---

The CPU execution target is the baseline path. GPU and NPU availability depends
on the device, platform capabilities, and execution backend installed for your
app.

## Configure your project

### [C#](#tab/csharp)

> [!IMPORTANT]
> The Windows ML package doesn't include C# APIs yet. For now, you generate the bindings with CsWin32 and copy a C# wrapper into your project by hand. A future update will include the C# APIs natively, and you can then remove the copied files.

The package includes Win32 metadata for the Runtime and Task APIs, `metadata\win32\Microsoft.Windows.AI.MachineLearning.Runtime.winmd`. CsWin32 generates unmanaged bindings from it. The package adds the metadata to CsWin32 and lets the generated imports load the Runtime DLLs from the application directory. Set `WinMLEnableRuntimeCsWin32Metadata` to `false` to opt out. The Server API isn't included in the metadata.

1. Add the package references. Set the runtime identifier to `win-x64` or `win-arm64` to match the process architecture, so that the package's native DLLs are placed next to your app.

   ```xml
   <PropertyGroup>
     <TargetFramework>net8.0-windows10.0.19041.0</TargetFramework>
     <RuntimeIdentifier>win-x64</RuntimeIdentifier>
     <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
   </PropertyGroup>
   <ItemGroup>
     <PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="2.9.36-experimental" />
     <PackageReference Include="Microsoft.Windows.CsWin32" Version="0.3.369" PrivateAssets="all" />
   </ItemGroup>
   ```

2. Copy the wrapper files from the [Windows ML samples repository](https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp) into your project:

   - The [`Runtime`](https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp/Runtime) folder, which holds the wrapper source (namespaces `WinMLSamples.Runtime` and `WinMLSamples.Runtime.Tasks`).
   - [`NativeMethods.txt`](https://github.com/microsoft/WindowsML/blob/main/Samples/Runtime/shared/csharp/NativeMethods.txt), which lists the APIs that CsWin32 generates bindings for.
   - [`NativeMethods.json`](https://github.com/microsoft/WindowsML/blob/main/Samples/Runtime/shared/csharp/NativeMethods.json), which sets the CsWin32 generation options.

The wrapper gives the COM-style APIs an idiomatic C# surface: objects implement `IDisposable`, failures throw .NET exceptions that keep the `HRESULT`, and token streams are `IAsyncEnumerable<T>`. Your code then has no pointers or `HRESULT` checks.

> [!NOTE]
> The wrapper is sample code, not a supported API. It isn't shipped in a package, and it can change with the samples. If you copy it, you maintain it until the C# APIs are included in the package.

### [C++](#tab/cpp)

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

### [Python](#tab/python)

Install the `windowsml` wheel. Because the command names an exact pre-release version, pip accepts it without `--pre` and keeps your other dependencies on their stable releases:

```console
pip install "windowsml==2.9.36a0"
```

Some features need extra packages that match the same version:

| Feature | Install |
|---|---|
| ONNX Runtime models, such as a composed Task model | `pip install "windowsml[with-ort]==2.9.36a0"` |
| GGUF models with llama.cpp, including `IWinMLLlamaModelLoader` | `pip install "windowsml-llama-core==2.9.36a0"` |

---

## Initialize the Runtime

### [C#](#tab/csharp)

Replace `Program.cs` with the following code:

```csharp
// Temporary: WinMLRuntime and the other wrapper types in the WinMLSamples.Runtime
// namespaces aren't in the Windows ML package yet. Copy the wrapper files from
// https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp
// into your project. A future update will include the C# APIs natively.
using WinMLSamples.Runtime;

using WinMLRuntime runtime = WinMLRuntime.Create();

Console.WriteLine("Created the Windows ML Runtime.");
```

Build for the architecture that matches the runtime identifier, such as `win-x64`. A successful build confirms that CsWin32 generated the bindings and that the package copied the Runtime DLLs beside the executable.

### [C++](#tab/cpp)

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

### [Python](#tab/python)

Create a file named `main.py` with the following code:

```python
from windowsml.runtime import Runtime

# Create the Windows ML Runtime. The context manager releases it when the block ends.
with Runtime() as runtime:
    print("Created the Windows ML Runtime.")
```

Run it with `python main.py`.

---

## Next steps

- [Run a model with the Windows ML Runtime API](./tutorial.md)
- [Windows ML Runtime API concepts](./concepts.md)
