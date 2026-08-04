---
title: Install and deploy Windows ML
description: Learn how to add Windows ML to your app using framework-dependent or self-contained deployment, across C#, C++/WinRT, C/C++, and Python.
ms.date: 07/28/2026
ms.topic: how-to
---

# Install and deploy Windows ML

Windows ML supports two deployment modes for the Windows ML Runtime: **self-contained** and **framework-dependent**. Choose based on your app's size, update, and dependency requirements.

To learn more about Windows ML, see [What is Windows ML](./overview.md).

## Quick start

Here's the short version of how to install Windows ML in your project:

### [C#](#tab/csharp)

For **self-contained (increases app size by ~41 MB, doesn't auto-update)**:

* For apps targeting Windows 10 Build 18362 or higher, install [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning)
* Or, for apps targeting Windows 10 Build 17763 or higher, install  [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML)
* Do not install `Microsoft.WindowsAppSDK.Runtime` or the main `Microsoft.WindowsAppSDK` package

For **framework-dependent (smaller app size, auto-updates)**, install:
* [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML)
* [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime)
* Then, see [Deployment for Windows App SDK framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for how to deploy the Windows App SDK runtime (which includes the Windows ML Runtime)

### [C++/WinRT](#tab/cppwinrt)

For **self-contained (increases app size by ~41 MB, doesn't auto-update)**:

* For apps targeting Windows 10 Build 18362 or higher, install [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning)
* Or, for apps targeting Windows 10 Build 17763 or higher, install  [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML)
* Do not install `Microsoft.WindowsAppSDK.Runtime` or the main `Microsoft.WindowsAppSDK` package

For **framework-dependent (smaller app size, auto-updates)**, install:
* [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML)
* [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime)
* Then, see [Deployment for Windows App SDK framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for how to deploy the Windows App SDK runtime (which includes the Windows ML Runtime)

### [C/C++](#tab/c)

For **self-contained** (framework-dependent is not supported for C/C++), reference [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) and configure CMake to use its `build/cmake` directory. See [Self-contained installation](#self-contained-installation) below for the full CMake setup.

### [Python](#tab/python)

For **framework-dependent**, install the required packages:

```powershell
pip install wasdk-Microsoft.Windows.AI.MachineLearning[all] wasdk-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap onnxruntime-windowsml
```

And then install the [Windows App SDK Runtime](/windows/apps/windows-app-sdk/downloads) that matches the version of the `wasdk-` packages.

---

For full details on each option, see the sections below.

## Requirements

### [C#](#tab/csharp)

* .NET 8 or greater
* Target framework moniker `net8.0-windows10.0.17763.0` or greater if using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML), or target framework moniker `net8.0-windows10.0.18362.0` or greater if using [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning)

> [!NOTE]
> With .NET 6, you can use the `Microsoft.Windows.AI.MachineLearning` namespace to install execution providers, but `Microsoft.ML.OnnxRuntime` APIs are not available. Upgrade to .NET 8 to access the full Windows ML API surface.

### [C++/WinRT](#tab/cppwinrt)

* C++20 or later

### [C/C++](#tab/c)

* Visual Studio 2022 with C++ workload
* CMake 3.21 or later

### [Python](#tab/python)

* Python 3.10–3.13 on x64 or ARM64
* An unpackaged Python installation (python.org or winget — not the Microsoft Store)

---

## What's in the Windows ML Runtime

The Windows ML Runtime is composed of these binaries:

| Binary | Description | Approx. size |
|---|---|---|
| `Microsoft.Windows.AI.MachineLearning.dll` | Windows ML APIs (`ExecutionProviderCatalog`, etc.) | ~1 MB |
| `onnxruntime.dll` | The ONNX Runtime engine | ~20 MB |
| `DirectML.dll` | DirectML — the included GPU execution provider | ~20 MB |
| | **Total** | **~41 MB** |

Vendor execution providers (QNN, VitisAI, OpenVINO, NvTensorRtRtx, MIGraphX) are not part of the Windows ML Runtime. They are obtained separately — either via the `ExecutionProviderCatalog` at runtime, or bundled yourself. See [Accelerate AI models](./accelerate-ai-models.md).

## Choosing a deployment mode

When using Windows ML, you can choose between two deployment modes for the Windows ML Runtime: **self-contained** and **framework-dependent**.

| | Self-contained | Framework-dependent |
|---|---|---|
| **Supported languages** | C#, C++/WinRT, C/C++ | C#, C++/WinRT, Python |
| **Supported packaging types** | Unpackaged and packaged (MSIX) | Unpackaged and packaged (MSIX) |
| **Enterprise deployment** | Supported | Supported |
| **App size** | Larger — Windows ML Runtime binaries are bundled with your app (~41 MB) | Smaller — Windows ML Runtime binaries are shared system-wide |
| **Windows ML updates** | Manual — you ship a new version when you choose | Automatic — users get updates via Windows App SDK servicing |
| **Dependency on installed runtime** | No — all dependencies are in your app package | Yes — Windows App SDK runtime must be present |
| **NuGet package (C#/C++)** | [`Microsoft.Windows.AI.MachineLearning`](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) or [`Microsoft.WindowsAppSDK.ML`](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) | [`Microsoft.WindowsAppSDK.ML`](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) + [`Microsoft.WindowsAppSDK.Runtime`](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime) |
| **Best for** | Strict version control | Apps that require a minimal download/install size; Apps shipping through the Microsoft Store |

## Self-contained installation

Apps can choose to self-contain the Windows ML Runtime binaries directly in the app. No external runtime dependency is required, but your app will be larger. This is the default when you reference `Microsoft.Windows.AI.MachineLearning` or `Microsoft.WindowsAppSDK.ML` without referencing the main `Microsoft.WindowsAppSDK` or `Microsoft.WindowsAppSDK.Runtime` package. If you're using the Windows App SDK packages, see [Deployment guide for self-contained apps](/windows/apps/package-and-deploy/self-contained-deploy/deploy-self-contained-apps) for details.

### [C#](#tab/csharp)

In your project, install or update the following NuGet packages:

* For apps targeting Windows 10 Build 18362 or higher:
  * Install [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) *(this adds the Windows ML APIs and binaries, defaults to self-contained)*
* Or, for apps targeting Windows 10 Build 17763 or higher:
  * Install [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) *(this adds the Windows ML APIs and binaries, defaults to self-contained, and adds RegFree WinRT support for 17763)*
* Do NOT install [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime) *(this would enable framework-dependent mode... remove it if you currently have it installed)*
* Do NOT install the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package *(this would enable framework-dependent mode... remove it if you currently have it installed)*
* Feel free to install other Windows App SDK component packages like [Microsoft.WindowsAppSDK.AI](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.AI), as long as you don't install the Runtime or the main package

> [!NOTE]
> If you must use the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package, update it to version 1.8.1 or greater, which includes Windows ML and all other Windows App SDK APIs, and in your `.csproj`, set `WindowsAppSDKSelfContained` to `true` to enable self-contained mode.


### [C++/WinRT](#tab/cppwinrt)

In your project, install or update the following NuGet packages:

* For apps targeting Windows 10 Build 18362 or higher:
  * Install [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) *(this adds the Windows ML APIs and binaries, defaults to self-contained)*
* Or, for apps targeting Windows 10 Build 17763 or higher:
  * Install [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) *(this adds the Windows ML APIs and binaries, defaults to self-contained, and adds RegFree WinRT support for 17763)*
* Do NOT install [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime) *(this would enable framework-dependent mode... remove it if you currently have it installed)*
* Do NOT install the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package *(this would enable framework-dependent mode... remove it if you currently have it installed)*
* Feel free to install other Windows App SDK component packages like [Microsoft.WindowsAppSDK.AI](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.AI), as long as you don't install the Runtime or the main package

> [!NOTE]
> If you must use the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package, update it to version 1.8.1 or greater, which includes Windows ML and all other Windows App SDK APIs, and in your project, set `WindowsAppSDKSelfContained` to `true` to enable self-contained mode.

### [C/C++](#tab/c)

The C APIs ship in the [`Microsoft.Windows.AI.MachineLearning` NuGet package](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning). The package includes CMake config files under `build/cmake/` for direct consumption.

To use the NuGet package...

1. Download or restore the NuGet package (e.g., via `nuget restore` or manual download)
2. Extract or reference the package root directory
3. Set `CMAKE_PREFIX_PATH` to the `build/cmake` subdirectory of the package

**Example CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.21)
project(MyApp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(microsoft.windows.ai.machinelearning CONFIG REQUIRED)

add_executable(MyApp main.cpp)
target_link_libraries(MyApp PRIVATE WindowsML::Api WindowsML::OnnxRuntime)

add_custom_command(TARGET MyApp POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        $<TARGET_RUNTIME_DLLS:MyApp> $<TARGET_FILE_DIR:MyApp>
    COMMAND_EXPAND_LISTS
    VERBATIM
)
```

**Example build commands**

```powershell
# Point CMAKE_PREFIX_PATH to the NuGet package's cmake directory
cmake -B build -S . -DCMAKE_PREFIX_PATH="C:/packages/Microsoft.Windows.AI.MachineLearning/build/cmake"
cmake --build build --config Release
```

The NuGet package ships its own cmake config under `build/cmake/` that understands
the NuGet directory structure (architecture-specific directories under `lib/native/`
and `runtimes/`). Architecture is determined from `CMAKE_GENERATOR_PLATFORM`,
`CMAKE_VS_PLATFORM_NAME`, or `CMAKE_SYSTEM_PROCESSOR`.

The vcpkg portfile generates a separate, thin config file that maps to the standard
vcpkg layout (`lib/` and `bin/`). Both configs share the same targets file
(`microsoft.windows.ai.machinelearning-targets.cmake`) which defines the IMPORTED
targets.

The same targets (`WindowsML::WindowsML`, `WindowsML::Api`, `WindowsML::OnnxRuntime`, `WindowsML::DirectML`) are available
in both consumption modes. Use the standard CMake 3.21 `$<TARGET_RUNTIME_DLLS>` generator expression
to copy runtime DLLs to the build output directory.

### [Python](#tab/python)

Self-contained deployment is not applicable for Python. Use the framework-dependent pip packages described above.

---

## Minimize self-contained app size by excluding DirectML

> [!NOTE]
> This configuration is not officially supported. It relies on the internal file layout of the [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) package, which could change in a future version. Test thoroughly after any package update.

The self-contained Windows ML Runtime bundles three binaries: the Windows ML API (`Microsoft.Windows.AI.MachineLearning.dll`), the ONNX Runtime engine (`onnxruntime.dll`), and the DirectML execution provider (`DirectML.dll`, ~20 MB). If your app only calls ONNX Runtime APIs directly and never requests the DirectML execution provider, you can drop `DirectML.dll` and roughly halve the self-contained footprint.

This works because Windows only loads a DLL at process start if your app's import table actually references one of its exports. If your code never requests the DirectML execution provider, `DirectML.dll` never ends up in your import table, so removing it after build doesn't break your app. If you later add a code path that does request it, add the binary back.

### [C#](#tab/csharp)

Follow the [self-contained installation](#self-contained-installation) steps for C#, then add a post-build target to your `.csproj` to remove the unused binary from the output directory. If you don't build or publish for a specific `RuntimeIdentifier`, the output also contains a copy of `DirectML.dll` under `runtimes\<rid>\native\` for each supported architecture (`win-x64`, `win-arm64`, `win-arm64ec`), so remove those too:

```xml
<Target Name="RemoveUnusedWindowsMLBinaries" AfterTargets="Build">
  <ItemGroup>
    <_UnusedDirectML Include="$(OutDir)DirectML.dll" Condition="Exists('$(OutDir)DirectML.dll')" />
    <_UnusedDirectML Include="$(OutDir)runtimes\win-*\native\DirectML.dll" />
  </ItemGroup>
  <Delete Files="@(_UnusedDirectML)" />
</Target>

<Target Name="RemoveUnusedWindowsMLBinariesFromPublish" AfterTargets="Publish">
  <ItemGroup>
    <_UnusedDirectMLPublish Include="$(PublishDir)DirectML.dll" Condition="Exists('$(PublishDir)DirectML.dll')" />
    <_UnusedDirectMLPublish Include="$(PublishDir)runtimes\win-*\native\DirectML.dll" />
  </ItemGroup>
  <Delete Files="@(_UnusedDirectMLPublish)" />
</Target>
```

### [C++/WinRT](#tab/cppwinrt)

Follow the [self-contained installation](#self-contained-installation) steps for C++/WinRT, then add a post-build event (**Project Properties** > **Build Events** > **Post-Build Event**, or an equivalent MSBuild target) to remove the unused binary from the output directory:

```xml
<Target Name="RemoveUnusedWindowsMLBinaries" AfterTargets="Build">
  <Delete Files="$(OutDir)DirectML.dll" />
</Target>
```

### [C/C++](#tab/c)

If you consume the package through CMake (see [Self-contained installation](#self-contained-installation)), `DirectML.dll` is already excluded by default: it has no import library, so it's never included in the standard `$<TARGET_RUNTIME_DLLS:MyApp>` post-build copy command, regardless of which `WindowsML::*` targets you link against. Only add it back if you use the DirectML execution provider, as shown in the comments of `microsoft.windows.ai.machinelearning-config.cmake`.

If you consume the package directly through MSBuild (a `.vcxproj` that references the NuGet package instead of using CMake), add a post-build event (**Project Properties** > **Build Events** > **Post-Build Event**, or an equivalent MSBuild target) to remove the unused binary from the output directory:

```xml
<Target Name="RemoveUnusedWindowsMLBinaries" AfterTargets="Build">
  <Delete Files="$(OutDir)DirectML.dll" />
</Target>
```

### [Python](#tab/python)

Not applicable — self-contained deployment isn't supported for Python. See [Framework-dependent installation](#framework-dependent-installation) instead.

---

## Framework-dependent installation

The framework-dependent version of the Windows ML Runtime is contained in the Windows App SDK runtime, which must be installed on the end user's machine. See [Deployment for Windows App SDK framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for details.

### [C#](#tab/csharp)

In your project, install or update the following NuGet packages:

* [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) *(this adds the Windows ML APIs)*
* [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime) *(this enables framework-dependent mode)*

> [!NOTE]
> Alternatively, install / update the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package to version 1.8.1 or greater, which includes Windows ML and all other Windows App SDK APIs, and defaults to framework-dependent.

Also, in your `.csproj` file, ensure that `WindowsAppSDKSelfContained` is either unspecified (not present), or `false`, so that it uses framework-dependent deployment.

### [C++/WinRT](#tab/cppwinrt)

In your project, install or update the following NuGet packages:

* [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) *(this adds the Windows ML APIs)*
* [Microsoft.WindowsAppSDK.Runtime](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.Runtime) *(this enables framework-dependent mode)*

> [!NOTE]
> Alternatively, install / update the main [Microsoft.WindowsAppSDK](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) NuGet package to version 1.8.1 or greater, which includes Windows ML and all other Windows App SDK APIs, and defaults to framework-dependent.

Also, in your project file, ensure that `WindowsAppSDKSelfContained` is either unspecified (not present), or `false`, so that it uses framework-dependent deployment.

### [C/C++](#tab/c)

Framework-dependent C/C++ support is not currently available. Either use C++/WinRT, or self-contained as described above.

### [Python](#tab/python)

Install the required packages:

```powershell
pip install wasdk-Microsoft.Windows.AI.MachineLearning[all] wasdk-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap onnxruntime-windowsml
```

And then install the [Windows App SDK Runtime](/windows/apps/windows-app-sdk/downloads) that matches the version of the `wasdk-` packages.

---

Finally, see [Deployment architecture for framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) to learn how to deploy the Windows App SDK runtime to your user's devices alongside your app.

## Next step

> [!div class="nextstepaction"]
> [Get started with Windows ML](./get-started.md)

## See also

* [What is Windows ML](./overview.md)
* [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)
