---
title: Get started with Windows ML
description: Learn how to use Windows ML to download and register AI execution providers for hardware-optimized inference.
ms.date: 03/20/2026
ms.topic: how-to
---

# Get started with Windows ML

This topic shows you how to install and use Windows ML to discover, download, and register execution providers (EPs) for use with the ONNX Runtime shipped with Windows ML. Windows ML handles the complexity of package management and hardware selection, allowing you to download the latest execution providers compatible with your users' hardware.

If you're not already familiar with the ONNX Runtime, we suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/). In short, Windows ML provides a copy of the ONNX Runtime, plus the ability to dynamically download execution providers (EPs).

## Prerequisites

* Version of Windows that [Windows App SDK supports](/windows/apps/windows-app-sdk/support)
* Language-specific prerequisites seen below

### [C#](#tab/csharp)

* .NET 8 or greater to use all Windows ML APIs
  * With .NET 6, you can install execution providers using the `Microsoft.Windows.AI.MachineLearning` APIs, but you cannot use the `Microsoft.ML.OnnxRuntime` APIs.
* Targeting a Windows 10-specific TFM like `net8.0-windows10.0.19041.0` or greater

### [C++/WinRT](#tab/cppwinrt)

C++20 or later.

### [C/C++](#tab/c)

* Visual Studio 2022 with C++ workload (includes the C compiler)
* CMake 3.21 or later

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Install or update Windows ML

### [C#](#tab/csharp)

Windows ML is included in [Windows App SDK 1.8.1 or greater](/windows/apps/windows-app-sdk/stable-channel).

The easiest way to use Windows ML is to install the [`Microsoft.WindowsAppSDK.ML` NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML), which uses self-contained deployment by default. See [Deploy your app](./distributing-your-app.md) to learn more about deployment options.

```bash
dotnet add package Microsoft.WindowsAppSDK.ML
```

### [C++/WinRT](#tab/cppwinrt)

Windows ML is included in [Windows App SDK 1.8.1 or greater](/windows/apps/windows-app-sdk/stable-channel).

See [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) for how to add the Windows App SDK to your project, or if you're already using Windows App SDK, update your packages.

### [C/C++](#tab/c)

Windows ML C APIs are included in the [`Microsoft.WindowsAppSDK.ML` NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) version `1.8.2141` or greater. The NuGet package ships CMake config files under `build/cmake/` that are directly consumable. The `@WINML_VERSION@` token is resolved at NuGet build time, so no additional processing is required.

To use the NuGet package...

1. Download or restore the NuGet package (e.g., via `nuget restore` or manual download)
2. Extract or reference the package root directory
3. Set `CMAKE_PREFIX_PATH` to the `build/cmake` subdirectory of the package

**Example CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.21)
project(MyApp LANGUAGES CXX)

# C++20 standard for modern features (std::format, etc.)
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

**Example Build Commands**

```powershell
# Point CMAKE_PREFIX_PATH to the NuGet package's cmake directory
cmake -B build -S . -DCMAKE_PREFIX_PATH="C:/packages/Microsoft.WindowsAppSDK.ML/build/cmake"
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

The Python binding leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project for the Windows App SDK projection. Ensure that your Python installation is not from the Microsoft Store (you can install an unpackaged version from python.org or via winget). The sample depends on using the Windows App SDK dynamic dependency API, which is only valid for unpackaged apps.

Please install the python packages with these commands:
```PowerShell
pip install wasdk-Microsoft.Windows.AI.MachineLearning[all] wasdk-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap onnxruntime-windowsml
```

Please make sure the `wasdk-` packages' version matches the WindowsAppRuntime's version.

---

## Step 2: Download and register EPs

### [C#](#tab/csharp)

The simplest way to get started is to let Windows ML automatically discover, download, and register the latest version of all compatible execution providers. Execution providers need to be registered with the ONNX Runtime inside of Windows ML before you can use them. And if they haven't been downloaded yet, they need to be downloaded first. Calling `EnsureAndRegisterCertifiedAsync()` will do both of these in one step.

```csharp
using Microsoft.ML.OnnxRuntime;
using Microsoft.Windows.AI.MachineLearning;

// First we create a new instance of EnvironmentCreationOptions
EnvironmentCreationOptions envOptions = new()
{
    logId = "WinMLDemo", // Use an ID of your own choice
    logLevel = OrtLoggingLevel.ORT_LOGGING_LEVEL_ERROR
};

// And then use that to create the ORT environment
using var ortEnv = OrtEnv.CreateInstanceWithOptions(ref envOptions);

// Get the default ExecutionProviderCatalog
var catalog = ExecutionProviderCatalog.GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
// This downloads any necessary components and registers them
await catalog.EnsureAndRegisterCertifiedAsync();
```

### [C++/WinRT](#tab/cppwinrt)

The simplest way to get started is to let Windows ML automatically discover, download, and register the latest version of all compatible execution providers. Execution providers need to be registered with the ONNX Runtime inside of Windows ML before you can use them. And if they haven't been downloaded yet, they need to be downloaded first. Calling `EnsureAndRegisterCertifiedAsync()` will do both of these in one step.

```cppwinrt
#include <winrt/Microsoft.Windows.AI.MachineLearning.h>
#include <winml/onnxruntime_cxx_api.h>

// First we need to create an ORT environment
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "WinMLDemo"); // Use an ID of your own choice

// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
catalog.EnsureAndRegisterCertifiedAsync().get();
```

### [C/C++](#tab/c)

With the C APIs, you must download a specific execution provider, and then manually register its path with ONNX Runtime.

```cpp
#include <WinMLEpCatalog.h>
#include <onnxruntime_cxx_api.h> // Or <onnxruntime_c_api.h> if just using C
#include <filesystem>
#include <string>

// Initialize ONNX Runtime
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "WinMLDemo"); // Use an ID of your own choice

// --- Windows ML: Get EP catalog handle ---
WinMLEpCatalogHandle catalog = nullptr;
HRESULT hr = WinMLEpCatalogCreate(&catalog);
if (FAILED(hr))
{
    // Handle error
    return;
}

// --- Windows ML: Find, download, and register a specific EP (in this case, QNN) ---
WinMLEpHandle ep = nullptr;
hr = WinMLEpCatalogFindProvider(catalog, "QNN", NULL, &ep);
if (SUCCEEDED(hr))
{
    // Windows ML: Download the EP if it isn't already downloaded
    WinMLEpEnsureReady(ep);

    // Windows ML: Get the EP library path
    size_t pathSize = 0;
    WinMLEpGetLibraryPathSize(ep, &pathSize);
    std::string libraryPathUtf8(pathSize, '\0');
    WinMLEpGetLibraryPath(ep, pathSize, libraryPathUtf8.data(), nullptr);

    // Register the EP with ONNX Runtime using the C++ API
    std::filesystem::path libraryPath(libraryPathUtf8);
    env.RegisterExecutionProviderLibrary("QNN", libraryPath.wstring());
}

// --- Windows ML: Release the EP catalog handle ---
WinMLEpCatalogRelease(catalog);
```

### [Python](#tab/python)

With the Python APIs, you can enumerate all compatible execution providers, and then manually register their paths with ONNX Runtime.

```python
import sys
from pathlib import Path
import traceback

_winml_instance = None

class WinML:
    def __new__(cls, *args, **kwargs):
        global _winml_instance
        if _winml_instance is None:
            _winml_instance = super(WinML, cls).__new__(cls, *args, **kwargs)
            _winml_instance._initialized = False
        return _winml_instance

    def __init__(self):
        if self._initialized:
            return
        self._initialized = True

        self._fix_winrt_runtime()
        from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
            InitializeOptions,
            initialize
        )
        import winui3.microsoft.windows.ai.machinelearning as winml
        self._win_app_sdk_handle = initialize(options=InitializeOptions.ON_NO_MATCH_SHOW_UI)
        self._win_app_sdk_handle.__enter__()
        catalog = winml.ExecutionProviderCatalog.get_default()
        self._providers = catalog.find_all_providers()
        self._ep_paths : dict[str, str] = {}
        for provider in self._providers:
            provider.ensure_ready_async().get()
            if provider.library_path == '':
                continue
            self._ep_paths[provider.name] = provider.library_path
        self._registered_eps : list[str] = []

    def __del__(self):
        self._providers = None
        self._win_app_sdk_handle.__exit__(None, None, None)

    def _fix_winrt_runtime(self):
        """
        This function removes the msvcp140.dll from the winrt-runtime package.
        So it does not cause issues with other libraries.
        """
        from importlib import metadata
        site_packages_path = Path(str(metadata.distribution('winrt-runtime').locate_file('')))
        dll_path = site_packages_path / 'winrt' / 'msvcp140.dll'
        if dll_path.exists():
            dll_path.unlink()

    def register_execution_providers_to_ort(self) -> list[str]:
        import onnxruntime as ort
        for name, path in self._ep_paths.items():
            if name not in self._registered_eps:
                try:
                    ort.register_execution_provider_library(name, path)
                    self._registered_eps.append(name)
                except Exception as e:
                    print(f"Failed to register execution provider {name}: {e}", file=sys.stderr)
                    traceback.print_exc()
        return self._registered_eps

WinML().register_execution_providers_to_ort()
```

---

> [!TIP]
> You can sometimes get better performance in ONNX Runtime by enabling thread spinning. See [Thread spinning behavior in Windows ML](./run-onnx-models.md#thread-spinning-behavior) for more info.

> [!TIP]
> In production applications, wrap the `EnsureAndRegisterCertifiedAsync()` call in a try-catch block to handle potential network or download failures gracefully.

## Next steps

After registering execution providers, you're ready to use the ONNX Runtime APIs within Windows ML! You will want to...

1. **[Select execution providers](./select-execution-providers.md)** - Tell the runtime which execution providers you want to use
2. **[Get your models](./model-catalog/overview.md)** - Use Model Catalog to dynamically download models, or include them locally
3. **[Run model inference](./run-onnx-models.md)** - Compile, load, and inference your model

## See also

* **[Model Catalog](./model-catalog/overview.md)** - Dynamically download models from online catalogs
* **[Install execution providers](./initialize-execution-providers.md)** - Additional ways you can handle download of EPs
* **[Distribute your app](./distributing-your-app.md)** - Info about distributing an app using Windows ML
* **[ONNX versions in Windows ML](./onnx-versions.md)** - Info about which ONNX Runtime version ships with Windows ML
* **[Tutorial](./tutorial.md)** - Full end-to-end tutorial using Windows ML with the ResNet-50 model
* **[Code samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)** - Our code samples using Windows ML
