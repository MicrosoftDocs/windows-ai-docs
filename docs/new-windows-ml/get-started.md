---
title: Get started with Windows ML
description: Learn how to use Windows ML to download and register AI execution providers for hardware-optimized inference.
ms.date: 08/13/2025
ms.topic: how-to
---

# Get started with Windows ML

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and *not* supported for use in a production environment. If you have an app trying out these APIs, then you should *not* publish it to the Microsoft Store.

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

Windows ML is included in the **framework-dependent** [Windows App SDK 1.8 **Experimental 4** release](/windows/apps/windows-app-sdk/experimental-channel).

### [C#](#tab/csharp)

See [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) for how to add the Windows App SDK to your project, or if you're already using Windows App SDK, update your packages.

* Make sure you **install the 1.8.0-Experimental4 version**, because the release versions don't contain Windows ML yet.
* Make sure you use the **framework-dependent** deployment option, since [Windows ML currently doesn't support self-contained deployment](./distributing-your-app.md).

### [C++](#tab/cppwinrt)

See [use the Windows App SDK in an existing project](/windows/apps/windows-app-sdk/use-windows-app-sdk-in-existing-project) for how to add the Windows App SDK to your project, or if you're already using Windows App SDK, update your packages.

* Make sure you **install the 1.8.0-Experimental4 version**, because the release versions don't contain Windows ML yet.
* Make sure you use the **framework-dependent** deployment option, since [Windows ML currently doesn't support self-contained deployment](./distributing-your-app.md).

### [Python](#tab/python)

The Python binding leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project for the Windows App SDK projection. For the 1.8 experimental 4 release, these packages are hosted in a private index and will upstream to pywinrt and publish to PyPI in future versions. 

You'll also need to install a special onnxruntime package with the auto-ep feature enabled (this feature will be available in a future onnxruntime release).

Create a `requirements.txt` file with the following content:

```text
--index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple
--extra-index-url https://pypi.org/simple
onnxruntime-winml==1.22.0.post2
winrt-runtime==3.2.1
winrt-Windows.Foundation==3.2.1
winrt-Windows.Foundation.Collections==3.2.1
winui3-Microsoft.Windows.AI.MachineLearning==1!1.8.250702007.dev4
winui3-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap==1!1.8.250702007.dev4
```

Then install using pip:

```bash
pip install -r requirements.txt
```

---

## Step 2: Download and register EPs

The simplest way to get started is to let Windows ML automatically discover, download, and register the latest version of all compatible execution providers. Execution providers need to be registered with the ONNX Runtime inside of Windows ML before you can use them. And if they haven't been downloaded yet, they need to be downloaded first. Calling `EnsureAndRegisterAllAsync()` will do both of these in one step.

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
await catalog.EnsureAndRegisterAllAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
#include <winrt/Microsoft.Windows.AI.MachineLearning.h>
#include <win_onnxruntime_cxx_api.h>

// First we need to create an ORT environment
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "WinMLDemo"); // Use an ID of your own choice

// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
catalog.EnsureAndRegisterAllAsync().get();
```

### [Python](#tab/python)

```python
# Known issue: import winrt.runtime will cause the TensorRTRTX execution provider to fail registration.
# As a workaround, please run pywinrt related code in a separate thread.

# winml.py
import json

def _get_ep_paths() -> dict[str, str]:
    from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
        InitializeOptions,
        initialize
    )
    import winui3.microsoft.windows.ai.machinelearning as winml
    eps = {}
    with initialize(options = InitializeOptions.ON_NO_MATCH_SHOW_UI):
        catalog = winml.ExecutionProviderCatalog.get_default()
        providers = catalog.find_all_providers()
        for provider in providers:
            provider.ensure_ready_async().get()
            eps[provider.name] = provider.library_path
            # DO NOT call provider.try_register in python. That will register to the native env.
    return eps

if __name__ == "__main__":
    eps = _get_ep_paths()
    print(json.dumps(eps))

# In your application code
import subprocess
import json
import sys
from pathlib import Path
import onnxruntime as ort

def register_execution_providers():
    worker_script = str(Path(__file__).parent / 'winml.py')
    result = subprocess.check_output([sys.executable, worker_script], text=True)
    paths = json.loads(result)
    for item in paths.items():
        ort.register_execution_provider_library(item[0], item[1])
    _ep_registered = True

register_execution_providers()
```

---

> [!TIP]
> In production applications, wrap the `EnsureAndRegisterAllAsync()` call in a try-catch block to handle potential network or download failures gracefully.

## Next steps

After registering execution providers, you're ready to use the ONNX Runtime APIs within Windows ML! You will want to...

1. **[Select execution providers](./select-execution-providers.md)** - Tell the runtime which execution providers you want to use
2. **[Run model inference](./run-onnx-models.md)** - Compile, load, and inference your model

## See also

* **[Initialize execution providers](./initialize-execution-providers.md)** - Additional ways you can handle download of EPs
* **[Distribute your app](./distributing-your-app.md)** - Info about distributing an app using Windows ML
* **[ONNX versions in Windows ML](./onnx-versions.md)** - Info about which ONNX Runtime version ships with Windows ML
* **[Tutorial](./tutorial.md)** - Full end-to-end tutorial using Windows ML with the ResNet-50 model
* **[Code samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)** - Our code samples using Windows ML
