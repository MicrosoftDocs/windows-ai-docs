---
title: Get started with Windows ML
description: Learn how to use Windows ML to download and register AI execution providers for hardware-optimized inference.
ms.date: 08/13/2025
ms.topic: how-to
---

# Get started with Windows ML

This topic shows you how to install and use Windows ML to discover, download, and register execution providers (EPs) for use with the ONNX Runtime shipped with Windows ML. Windows ML handles the complexity of package management and hardware selection, automatically downloading the latest execution providers compatible with your device's hardware.

If you're not already familiar with the ONNX Runtime, we suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/). In short, Windows ML provides a shared Windows-wide copy of the ONNX Runtime, plus the ability to dynamically download execution providers (EPs).

## Prerequisites

* Windows 11 PC running version 24H2 (build 26100) or greater
* Language-specific prerequisites seen below

### [C#](#tab/csharp)

* .NET 6 or greater
* Targeting a Windows 10-specific TFM like `net6.0-windows10.0.19041.0` or greater

### [C++](#tab/cppwinrt)

C++20 or later.

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Install or update the Windows App SDK

The Model Catalog APIs are included in the **experimental** version of [Windows App SDK 2.0.0 or greater](/windows/apps/windows-app-sdk/experimental-channel).

### [C#](#tab/csharp)

See [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) for how to add the Windows App SDK to your project, or if you're already using Windows App SDK, update your packages.

### [C++](#tab/cppwinrt)

See [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) for how to add the Windows App SDK to your project, or if you're already using Windows App SDK, update your packages.

### [Python](#tab/python)

The Python binding leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project for the Windows App SDK projection.

Please install the python packages with these commands:
```PowerShell
pip install wasdk-Microsoft.Windows.AI.MachineLearning[all] wasdk-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap
# The onnxruntime-winml package is not published to PyPI yet. Please install it from the ort-nightly feed
pip install --pre --index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple/ --extra-index-url https://pypi.org/simple onnxruntime-winml
```

Please make sure the `wasdk-` packages' version matches the WindowsAppRuntime's version.

---

## Step 2: Download and register EPs

The simplest way to get started is to let Windows ML automatically discover, download, and register the latest version of all compatible execution providers. Execution providers need to be registered with the ONNX Runtime inside of Windows ML before you can use them. And if they haven't been downloaded yet, they need to be downloaded first. Calling `EnsureAndRegisterCertifiedAsync()` will do both of these in one step.

### [C#](#tab/csharp)

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

### [C++](#tab/cppwinrt)

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

### [Python](#tab/python)

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
> In production applications, wrap the `EnsureAndRegisterCertifiedAsync()` call in a try-catch block to handle potential network or download failures gracefully.

## Next steps

After registering execution providers, you're ready to use the ONNX Runtime APIs within Windows ML! You will want to...

1. **[Select execution providers](./select-execution-providers.md)** - Tell the runtime which execution providers you want to use
2. **[Get your models](./model-catalog/overview.md)** - Use Model Catalog to dynamically download models, or include them locally
3. **[Run model inference](./run-onnx-models.md)** - Compile, load, and inference your model

[!INCLUDE [C# tensors issue](./includes/csharp-tensors-issue.md)]

## See also

* **[Model Catalog](./model-catalog/overview.md)** - Dynamically download models from online catalogs
* **[Initialize execution providers](./initialize-execution-providers.md)** - Additional ways you can handle download of EPs
* **[Distribute your app](./distributing-your-app.md)** - Info about distributing an app using Windows ML
* **[ONNX versions in Windows ML](./onnx-versions.md)** - Info about which ONNX Runtime version ships with Windows ML
* **[Tutorial](./tutorial.md)** - Full end-to-end tutorial using Windows ML with the ResNet-50 model
* **[Code samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)** - Our code samples using Windows ML
