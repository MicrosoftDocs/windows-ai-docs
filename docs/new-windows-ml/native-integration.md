---
title: Use Windows ML without Windows App SDK
description: Learn how to use Windows ML in native C++ applications, middleware, and game engines without requiring the Windows App SDK, using vcpkg and the Windows ML C API.
ms.date: 02/05/2026
ms.topic: how-to
---

# Use Windows ML without Windows App SDK

Windows ML can be used in native C and C++ applications without any dependency on the Windows App SDK. This is ideal for middleware libraries, game engines, audio plugins, and cross-platform codebases that need to discover and use hardware-accelerated execution providers (EPs) on Windows.

The Windows ML C API (`WinMLEpCatalog.h`) provides EP discovery and acquisition using familiar C conventions: UTF-8 strings, HRESULT error codes, and callback-based patterns. After discovering EPs, you use the standard [ONNX Runtime C API](https://onnxruntime.ai/docs/api/) for model inference.

## When to use this approach

Use the Windows ML C API and vcpkg distribution when:

- Your codebase is C or C++ without C++/WinRT
- You're building cross-platform middleware with a Windows backend
- You need to minimize binary size and external dependencies
- You're shipping a plugin (for example, audio, video) that runs inside a host application you don't control
- Your build system uses CMake or another non-MSBuild toolchain

## Prerequisites

- Windows 11 (version 24H2, build 26100 or later)
- Visual Studio 2022 with C++ workload
- CMake 3.20 or later
- [vcpkg](https://vcpkg.io) (or the NuGet package `Microsoft.WindowsAppSDK.ML`)

## Step 1: Install the vcpkg package

The `microsoft-windows-ai-machinelearning` vcpkg port provides the Windows ML C API header, ONNX Runtime headers and libraries, and the runtime DLLs.

```powershell
vcpkg install microsoft-windows-ai-machinelearning
```

In your `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.20)
project(MyApp LANGUAGES CXX)

find_package(microsoft-windows-ai-machinelearning CONFIG REQUIRED)

add_executable(MyApp main.cpp)
target_link_libraries(MyApp PRIVATE Microsoft::Windows::AI::MachineLearning)

# Copy runtime DLLs to the output directory
winml_copy_runtime_dlls(MyApp)
```

Build with:

```powershell
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE="path/to/vcpkg/scripts/buildsystems/vcpkg.cmake"
cmake --build build --config Release
```

> [!TIP]
> You can also consume Windows ML from the `Microsoft.WindowsAppSDK.ML` NuGet package directly. The NuGet package includes the same headers, import libraries, and CMake config files as the vcpkg port.

## Step 2: Discover and prepare execution providers

Use the Windows ML C API to discover which execution providers are available for the current hardware and ensure they are ready to use.

```cpp
#include <WinMLEpCatalog.h>
#include <iostream>
#include <string>

int main()
{
    // Create a catalog handle
    WinMLEpCatalogHandle catalog = nullptr;
    HRESULT hr = WinMLEpCatalogCreate(&catalog);
    if (FAILED(hr))
    {
        return hr;
    }

    // Find a provider by name (NULL = any certified package)
    WinMLEpHandle ep = nullptr;
    hr = WinMLEpCatalogFindProvider(catalog, "QNN", NULL, &ep);
    if (FAILED(hr))
    {
        std::cerr << "QNN provider not found (0x" << std::hex << hr << ")" << std::endl;
        WinMLEpCatalogRelease(catalog);
        return hr;
    }

    // Ensure the provider is ready (downloads if needed)
    hr = WinMLEpEnsureReady(ep);
    if (FAILED(hr))
    {
        std::cerr << "Failed to ready provider (0x" << std::hex << hr << ")" << std::endl;
        WinMLEpCatalogRelease(catalog);
        return hr;
    }

    // Get the library path for ONNX Runtime registration
    size_t pathSize = 0;
    WinMLEpGetLibraryPathSize(ep, &pathSize);

    std::string libraryPath(pathSize, '\0');
    WinMLEpGetLibraryPath(ep, pathSize, libraryPath.data(), nullptr);

    std::cout << "Provider ready at: " << libraryPath << std::endl;

    WinMLEpCatalogRelease(catalog);
    return 0;
}
```

### Enumerating all providers

To discover all available providers, use the callback-based enumeration API:

```cpp
#include <WinMLEpCatalog.h>
#include <iostream>

BOOL CALLBACK PrintProvider(WinMLEpHandle ep, const WinMLEpInfo* info, void* context)
{
    std::cout << "Provider: " << info->name << " (v" << info->version << ")" << std::endl;
    std::cout << "  Ready: "
              << (info->readyState == WinMLEpReadyState_Ready ? "Yes" :
                  info->readyState == WinMLEpReadyState_NotReady ? "No" : "Not present")
              << std::endl;
    return TRUE; // Continue enumeration
}

// Usage:
// WinMLEpCatalogEnumProviders(catalog, PrintProvider, nullptr);
```

## Step 3: Register with ONNX Runtime and run inference

After getting the EP library path from Windows ML, register it with ONNX Runtime and create an inference session:

```cpp
#include <WinMLEpCatalog.h>
#include <onnxruntime_cxx_api.h>
#include <string>

int main()
{
    // Initialize ONNX Runtime
    Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "MyApp");

    // --- Windows ML: discover and prepare EP ---
    WinMLEpCatalogHandle catalog = nullptr;
    WinMLEpCatalogCreate(&catalog);

    WinMLEpHandle ep = nullptr;
    HRESULT hr = WinMLEpCatalogFindProvider(catalog, "QNN", NULL, &ep);
    if (SUCCEEDED(hr))
    {
        WinMLEpEnsureReady(ep);

        size_t pathSize = 0;
        WinMLEpGetLibraryPathSize(ep, &pathSize);
        std::string libraryPath(pathSize, '\0');
        WinMLEpGetLibraryPath(ep, pathSize, libraryPath.data(), nullptr);

        // Register the EP with ONNX Runtime
        const OrtApi* ortApi = OrtGetApiBase()->GetApi(ORT_API_VERSION);
        OrtStatus* status = ortApi->RegisterExecutionProviderLibrary(
            env, "QNN", libraryPath.c_str());
        if (status)
        {
            ortApi->ReleaseStatus(status);
        }
    }

    WinMLEpCatalogRelease(catalog);

    // --- ONNX Runtime: create session and run inference ---
    Ort::SessionOptions sessionOptions;
    Ort::Session session(env, L"model.onnx", sessionOptions);

    // ... run inference using standard ONNX Runtime APIs ...

    return 0;
}
```

## Handle ownership model

All provider handles (`WinMLEpHandle`) are owned by the catalog and remain valid until you call `WinMLEpCatalogRelease`. You don't need to release individual provider handles.

## Deployment

### Self-contained deployment

By default, include the runtime DLLs alongside your executable:

```
MyApp/
├── MyApp.exe
├── Microsoft.Windows.AI.MachineLearning.dll
├── onnxruntime.dll
├── onnxruntime_providers_shared.dll
└── DirectML.dll
```

If you use CMake with the vcpkg port, the `winml_copy_runtime_dlls` function handles copying these DLLs to your output directory automatically. You manage runtime updates through vcpkg or NuGet package updates.

### Using with Windows App SDK framework-dependent apps

If your application already bootstraps the Windows App SDK (via `MddBootstrapInitialize` or MSIX package identity), you can use the vcpkg port **without bundling the runtime DLLs**. The Windows App SDK framework package already contains `onnxruntime.dll`, `DirectML.dll`, and the other runtime binaries.

On Windows 11 (version 21H2, build 22000 and later), the [DLL search order](/windows/win32/dlls/dynamic-link-library-search-order) includes the process's **package dependency graph** — the set of framework packages added at bootstrap or declared in the app's package manifest. When the vcpkg port's import library triggers a load of `onnxruntime.dll`, Windows resolves it from the framework package automatically. No auto-initializer, CMake configuration changes, or additional NuGet packages are required.

To use this approach, omit the `winml_copy_runtime_dlls` call in your `CMakeLists.txt`:

```cmake
find_package(microsoft-windows-ai-machinelearning CONFIG REQUIRED)

add_executable(MyApp main.cpp)
target_link_libraries(MyApp PRIVATE WindowsML::Api WindowsML::OnnxRuntime)

# Do not call winml_copy_runtime_dlls — DLLs resolve from the framework package
```

> [!NOTE]
> This approach requires the Windows App SDK framework package to be present on the target machine. For apps that don't use the Windows App SDK, use [self-contained deployment](#self-contained-deployment) instead.

## Comparison with Windows App SDK

| | Windows App SDK | Self-contained (vcpkg / NuGet redist) |
|--|--|--|
| **API** | WinRT (`ExecutionProviderCatalog`) and C API | C API (`WinMLEpCatalog.h`) |
| **Languages** | C#, C++/WinRT, Python | C, C++ |
| **Build system** | MSBuild, NuGet | CMake, vcpkg, NuGet |
| **Runtime updates** | Automatic via shared framework | Manual via package updates |
| **Windows App SDK dependency** | Yes (framework-dependent) or optional (self-contained) | None (or optional — see [framework-dependent apps](#using-with-windows-app-sdk-framework-dependent-apps)) |
| **Best for** | Windows apps (WinUI, WPF, console) | Middleware, plugins, game engines, cross-platform libraries |

## See also

- [Get started with Windows ML](./get-started.md) — for Windows App SDK users
- [Supported execution providers](./supported-execution-providers.md)
- [Initialize execution providers](./initialize-execution-providers.md) — WinRT API patterns
- [Deploying your app](./distributing-your-app.md) — all deployment options
- [ONNX Runtime documentation](https://onnxruntime.ai/docs/)
