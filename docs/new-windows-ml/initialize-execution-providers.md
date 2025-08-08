---
title: Initialize execution providers with Windows ML
description: Learn advanced topics about downloading and registering AI execution providers using Windows Machine Learning (ML) for hardware-optimized inference.
ms.date: 08/08/2025
ms.topic: how-to
---

# Initialize execution providers with Windows ML

This page discusses more advanced ways your app can gracefully handle downloading and registering execution providers (EPs) using Windows ML. Even if an EP is already downloaded on the device, you must register the EPs every time your app runs so they will appear in ONNX Runtime.

## Download and register in one call

For initial development, it can be nice to simply call `EnsureAndRegisterAllAsync()`, which will download any new EPs if they're not already downloaded, and then register all the EPs. Note that on first run, this method can take multiple seconds or even minutes depending on your network speed and EPs that need to be downloaded.

### [C#](#tab/csharp)

```csharp
// Get the default ExecutionProviderCatalog
var catalog = ExecutionProviderCatalog.GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
// This downloads any necessary components and registers them
await catalog.EnsureAndRegisterAllAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
catalog.EnsureAndRegisterAllAsync().get();
```

---

## Register existing providers only

If you want to avoid downloading and only register execution providers that are already present on the machine:

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();

// Register only providers already present on the machine
// This avoids potentially long download times
await catalog.RegisterAllAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Register only providers already present on the machine
catalog.RegisterAllAsync().get();
```

---

## Discover if there are new EPs (without downloading)

If you want to see if there are new EPs available to download, but don't want to start the download, you can use the `FindAllProviders()` method and then see if any providers have a **ReadyState** of **NotPresent**. You can then decide to handle this however you would like (launching your users into an "Updating screen", asking them if they want to update, etc). You can choose to continue using the already-downloaded EPs (by calling `RegisterAllAsync()` as shown above) if you don't want to make your users wait right now.

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();

// Check if there are new EPs that need to be downloaded
if (catalog.FindAllProviders().Any(provider => provider.ReadyState == ExecutionProviderReadyState.NotPresent))
{
    // TODO: There are new EPs, decide how your app wants to handle that
}
else
{
    // All EPs are already present, just register them
    await catalog.RegisterAllAsync();
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

// Check if any providers need to be downloaded
bool needsDownload = false;
for (const auto& provider : providers)
{
    if (provider.ReadyState() == winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderReadyState::NotPresent)
    {
        needsDownload = true;
        break;
    }
}

if (needsDownload)
{
    // TODO: There are new EPs, decide how your app wants to handle that
}
else
{
    // All EPs are already present, just register them
    catalog.RegisterAllAsync().get();
}
```

## Production app example

For production applications, here's an example of what your app might want to do to give yourself and your users users control over when downloads occur. You can check if new execution providers are available and conditionally download them:

### [C#](#tab/csharp)

```csharp
using Microsoft.Windows.AI.MachineLearning;

var catalog = ExecutionProviderCatalog.GetDefault();

// Check if there are new EPs that need to be downloaded
if (catalog.FindAllProviders().Any(provider => provider.ReadyState == ExecutionProviderReadyState.NotPresent))
{
    // Show UI to user asking if they want to download new execution providers
    bool userWantsToDownload = await ShowDownloadDialogAsync();
    
    if (userWantsToDownload)
    {
        // Download and register all EPs
        await catalog.EnsureAndRegisterAllAsync();
    }
    else
    {
        // Register only already-present EPs
        await catalog.RegisterAllAsync();
    }
}
else
{
    // All EPs are already present, just register them
    await catalog.RegisterAllAsync();
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

// Check if any providers need to be downloaded
bool needsDownload = false;
for (const auto& provider : providers)
{
    if (provider.ReadyState() == winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderReadyState::NotPresent)
    {
        needsDownload = true;
        break;
    }
}

if (needsDownload)
{
    // Show UI to user or check application settings
    bool userWantsToDownload = ShowDownloadDialog();
    
    if (userWantsToDownload)
    {
        catalog.EnsureAndRegisterAllAsync().get();
    }
    else
    {
        catalog.RegisterAllAsync().get();
    }
}
else
{
    // All EPs are already present
    catalog.RegisterAllAsync().get();
}
```

---

## Python developers

For Python, registration is done manually as shown in the basic example below.
There is no direct equivalent to the methods above in the Python environment.

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