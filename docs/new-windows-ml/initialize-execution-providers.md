---
title: Initialize execution providers with Windows ML
description: Learn advanced topics about downloading and registering AI execution providers using Windows Machine Learning (ML) for hardware-optimized inference.
ms.date: 08/08/2025
ms.topic: how-to
---

# Initialize execution providers with Windows ML

This page discusses more advanced ways your app can gracefully handle downloading and registering execution providers (EPs) using Windows ML. Even if an EP is already downloaded on the device, you must register the EPs every time your app runs so they will appear in ONNX Runtime.

## Download and register in one call

For initial development, it can be nice to simply call `EnsureAndRegisterCertifiedAsync()`, which will download any new EPs (or new versions of EPs) that are compatible with your device and drivers if they're not already downloaded, and then register all the EPs. Note that on first run, this method can take multiple seconds or even minutes depending on your network speed and EPs that need to be downloaded.

### [C#](#tab/csharp)

```csharp
// Get the default ExecutionProviderCatalog
var catalog = ExecutionProviderCatalog.GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
// This downloads any necessary components and registers them
await catalog.EnsureAndRegisterCertifiedAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
catalog.EnsureAndRegisterCertifiedAsync().get();
```

### [Python](#tab/python)

```python
# Please DO NOT use this API. It won't register EPs to the python ort env.
```

---

> [!TIP]
> In production applications, wrap the `EnsureAndRegisterCertifiedAsync()` call in a try-catch block to handle potential network or download failures gracefully.

## Register existing providers only

If you want to avoid downloading and only register execution providers that are already present on the machine:

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();

// Register only providers already present on the machine
// This avoids potentially long download times
await catalog.RegisterCertifiedAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Register only providers already present on the machine
catalog.RegisterCertifiedAsync().get();
```

### [Python](#tab/python)

```python
# Please DO NOT use this API. It won't register EPs to the python ort env.
```

---

## Discover if there are new EPs (without downloading)

If you want to see if there are new EPs compatible with your device and drivers available to download, but don't want to start the download, you can use the `FindAllProviders()` method and then see if any providers have a **ReadyState** of **NotPresent**. You can then decide to handle this however you would like (launching your users into an "Updating screen", asking them if they want to update, etc). You can choose to continue using the already-downloaded EPs (by calling `RegisterCertifiedAsync()` as shown above) if you don't want to make your users wait right now.

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
    await catalog.RegisterCertifiedAsync();
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
    catalog.RegisterCertifiedAsync().get();
}
```

### [Python](#tab/python)

```python
# winml: winui3.microsoft.windows.ai.machinelearning
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()
if any(provider.ready_state == winml.ExecutionProviderReadyState.NOT_PRESENT for provider in providers):
    # TODO: There are new EPs, decide how your app wants to handle that
    pass
else:
    # Register the providers one by one
    for provider in providers:
        provider.ensure_ready_async().get()
        ort.register_execution_provider_library(provider.name, provider.library_path)
```

---

## Download and register a specific EP

If there is a specific execution provider your app wants to use, you can download and register a particular execution provider without downloading all compatible EPs.

You'll first use `FindAllProviders()` to get all compatible EPs, and then you can call `EnsureReadyAsync()` on a particular **ExecutionProvider** to download the specific execution provider, and call `TryRegister()` to register the specific execution provider.

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();

// Get the QNN provider, if present
var qnnProvider = catalog.FindAllProviders()
    .FirstOrDefault(i => i.Name == "QNNExecutionProvider");

if (qnnProvider != null)
{
    // Download it
    var result = await qnnProvider.EnsureReadyAsync();

    // If download succeeded
    if (result != null && result.Status == ExecutionProviderReadyResultState.Success)
    {
        // Register it
        bool registered = qnnProvider.TryRegister();
    }
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Get the QNN provider, if present
auto providers = catalog.FindAllProviders();
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProvider qnnProvider{ nullptr };
for (auto const& p : providers)
{
    if (p.Name() == L"QNNExecutionProvider")
    {
        qnnProvider = p;
        break;
    }
}

if (qnnProvider)
{
    // Download required components for this provider (if not already present)
    auto result = qnnProvider.EnsureReadyAsync().get();

    if (result && result.Status() == winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderReadyResultState::Success)
    {
        // Register the provider with ONNX Runtime
        bool registered = qnnProvider.TryRegister();
    }
}
```

### [Python](#tab/python)

```python
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()

qnn_provider = next((provider for provider in providers if provider.name == 'QNNExecutionProvider'), None)
if qnn_provider is not None:
    # Download required components for this provider (if not already present)
    result = qnn_provider.ensure_ready_async().get()
    if result == winml.ExecutionProviderReadyResultState.SUCCESS:
        # Register the provider with ONNX Runtime
        ort.register_execution_provider_library(qnn_provider.name, qnn_provider.library_path)
```

---

## Production app example

For production applications, here's an example of what your app might want to do to give yourself and your users users control over when downloads occur. You can check if new execution providers are available and conditionally download them:

### [C#](#tab/csharp)

```csharp
using Microsoft.Windows.AI.MachineLearning;

var catalog = ExecutionProviderCatalog.GetDefault();

// Filter to the EPs our app supports/uses
var providers = catalog.FindAllProviders().Where(p =>
    p.Name == "VitisAIExecutionProvider" ||
    p.Name == "OpenVINOExecutionProvider" ||
    p.Name == "QNNExecutionProvider" ||
    p.Name == "NvTensorRtRtxExecutionProvider"
);

if (providers.Any(p => p.ReadyState == ExecutionProviderReadyState.NotPresent))
{
    // Show UI to user asking if they want to download new execution providers
    bool userWantsToDownload = await ShowDownloadDialogAsync();

    if (userWantsToDownload)
    {
        // Download all EPs
        foreach (var p in providers)
        {
            if (p.ReadyState == ExecutionProviderReadyState.NotPresent)
            {
                // Ignore result handling here; production code could inspect status
                await p.EnsureReadyAsync();
            }
        }

        // And register all EPs
        await catalog.RegisterCertifiedAsync();
    }
    else
    {
        // Register only already-present EPs
        await catalog.RegisterCertifiedAsync();
    }
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
using namespace winrt::Microsoft::Windows::AI::MachineLearning;

auto catalog = ExecutionProviderCatalog::GetDefault();
auto allProviders = catalog.FindAllProviders();

// Filter to the EPs our app supports/uses
std::vector<ExecutionProvider> targetProviders;
for (auto const& p : allProviders)
{
    auto name = p.Name();
    if (name == L"VitisAIExecutionProvider" ||
        name == L"OpenVINOExecutionProvider" ||
        name == L"QNNExecutionProvider" ||
        name == L"NvTensorRtRtxExecutionProvider")
    {
        targetProviders.push_back(p);
    }
}

bool needsDownload = false;
for (auto const& p : targetProviders)
{
    if (p.ReadyState() == ExecutionProviderReadyState::NotPresent)
    {
        needsDownload = true;
        break;
    }
}

if (needsDownload)
{
    // Show UI to user or check application settings to confirm download
    bool userWantsToDownload = ShowDownloadDialog();
    if (userWantsToDownload)
    {
        // Download only the missing target providers
        for (auto const& p : targetProviders)
        {
            if (p.ReadyState() == ExecutionProviderReadyState::NotPresent)
            {
                // Ignore result handling here; production code could inspect status
                p.EnsureReadyAsync().get();
            }
        }
        // Register all (both previously present and newly downloaded) providers
        catalog.RegisterCertifiedAsync().get();
    }
    else
    {
        // User deferred download; register only already-present providers
        catalog.RegisterCertifiedAsync().get();
    }
}
else
{
    // All target EPs already present
    catalog.RegisterCertifiedAsync().get();
}
```

### [Python](#tab/python)

```python
# remove the msvcp140.dll from the winrt-runtime package.
# So it does not cause issues with other libraries.
from pathlib import Path
from importlib import metadata
site_packages_path = Path(str(metadata.distribution('winrt-runtime').locate_file('')))
dll_path = site_packages_path / 'winrt' / 'msvcp140.dll'
if dll_path.exists():
    dll_path.unlink()

from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
    InitializeOptions,
    initialize
)
import winui3.microsoft.windows.ai.machinelearning as winml
import onnxruntime as ort

with initialize(options=InitializeOptions.ON_NO_MATCH_SHOW_UI):
    catalog = winml.ExecutionProviderCatalog.get_default()
    # Filter EPs that the app supports
    providers = [provider for provider in catalog.find_all_providers() if provider.name in [
        'VitisAIExecutionProvider',
        'OpenVINOExecutionProvider',
        'QNNExecutionProvider',
        'NvTensorRtRtxExecutionProvider'
    ]]

    # Download and make ready missing EPs if the user wants to
    if any(provider.ready_state == winml.ExecutionProviderReadyState.NOT_PRESENT for provider in providers):
        # Ask the user if they want to download the missing packages
        if user_wants_to_download:
            for provider in [provider for provider in providers if provider.ready_state == winml.ExecutionProviderReadyState.NOT_PRESENT]:
                provider.ensure_ready_async().get()

    # Make ready the existing EPs
    for provider in [provider for provider in providers if provider.ready_state == winml.ExecutionProviderReadyState.NOT_READY]:
        provider.ensure_ready_async().get()

    # Register all ready EPs
    for provider in [provider for provider in providers if provider.ready_state == winml.ExecutionProviderReadyState.READY]:
        ort.register_execution_provider_library(provider.name, provider.library_path)
```
