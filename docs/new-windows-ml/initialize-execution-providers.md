---
title: Install execution providers with Windows ML
description: Learn how to download and install AI execution providers using Windows Machine Learning (ML) for hardware-optimized inference.
ms.date: 02/13/2026
ms.topic: how-to
---

# Install execution providers with Windows ML

With Windows ML, certain execution providers (EPs) are dynamically downloaded, installed, and shared system-wide via the Windows ML `ExecutionProviderCatalog` APIs, and are [automatically updated](./update-execution-providers.md). To see what EPs are available, see [Supported execution providers](./supported-execution-providers.md).

This page covers how to install EPs onto a user's device. Once installed, you'll need to [register execution providers](./register-execution-providers.md) with ONNX Runtime before using them.

## Install all compatible EPs

For initial development, it can be nice to simply call `EnsureAndRegisterCertifiedAsync()`, which will download and install all EPs available to your user's device, and then registers all EPs with the ONNX Runtime in one single call. Note that on first run, this method can take multiple seconds or even minutes depending on your network speed and EPs that need to be downloaded.

### [C#](#tab/csharp)

```csharp
// Get the default ExecutionProviderCatalog
var catalog = ExecutionProviderCatalog.GetDefault();

// Ensure execution providers compatible with device are present (downloads if necessary)
// and then registers all present execution providers with ONNX Runtime
await catalog.EnsureAndRegisterCertifiedAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure execution providers compatible with device are present (downloads if necessary)
// and then registers all present execution providers with ONNX Runtime
catalog.EnsureAndRegisterCertifiedAsync().get();
```

### [Python](#tab/python)

```python
# Please DO NOT use this API. It won't register EPs to the python ort env.
```

---

## Find all compatible EPs

You can see which EPs (including non-installed EPs) are available to the user's device by calling the `FindAllProviders()` method.

### [C#](#tab/csharp)

```csharp
ExecutionProviderCatalog catalog = ExecutionProviderCatalog.GetDefault();

// Find all available EPs (including non-installed EPs)
ExecutionProvider[] providers = catalog.FindAllProviders();

foreach (var provider in providers)
{
    Console.WriteLine($"{provider.Name}: {provider.ReadyState}");
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = ExecutionProviderCatalog::GetDefault();

// Find all available EPs (including non-installed EPs)
auto providers = catalog.FindAllProviders();

for (auto const& provider : providers)
{
    std::wcout << provider.Name() << L": " << static_cast<int>(provider.ReadyState()) << std::endl;
}
```

### [Python](#tab/python)

```python
# winml: winui3.microsoft.windows.ai.machinelearning
catalog = winml.ExecutionProviderCatalog.get_default()

# Find all available EPs (including non-installed EPs)
providers = catalog.find_all_providers()

for provider in providers:
    print(f"{provider.name}: {provider.ready_state}")
```

---

The execution providers returned will vary based on the user's device and available execution providers. On a compatible Qualcomm device without any execution providers currently installed, the above code outputs the following...

```output
QNNExecutionProvider: NotPresent
```

[!INCLUDE [Explanation of EP ReadyState values](./includes/ep-ready-states.md)]

## Install a specific EP

If there is a specific **ExecutionProvider** your app wants to use, and its **ReadyState** is `NotPresent`, you can download and install it by calling `EnsureReadyAsync()`.

### [C#](#tab/csharp)

```csharp
// Download and install a NotPresent EP
var result = await provider.EnsureReadyAsync();

// Check that the download and install was successful
bool installed = result.Status == ExecutionProviderReadyResultState.Success;
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Download and install a NotPresent EP
auto result = provider.EnsureReadyAsync().get();

// Check that the download and install was successful
bool installed = result.Status() == ExecutionProviderReadyResultState::Success;
```

### [Python](#tab/python)

```python
# Download and install a NotPresent EP
result = provider.ensure_ready_async().get()

# Check that the download and install was successful
installed = result.status == winml.ExecutionProviderReadyResultState.SUCCESS
```

---

## Installing with progress

The APIs for downloading and installing EPs include callbacks that provide progress updates, so that you can display progress indicators to keep your users informed.

![Screenshot of a progress bar reflecting download progress](../images/winml-acquireepprogress.jpg)

### [C#](#tab/csharp)

```csharp
// Start the download and install of a NotPresent EP
var operation = provider.EnsureReadyAsync();

// Listen to progress callback
operation.Progress = (asyncInfo, progressInfo) =>
{
    // Dispatch to UI thread (varies based on UI platform)
    _dispatcherQueue.TryEnqueue(() =>
    {
        // progressInfo is out of 100, convert to 0-1 range
        double normalizedProgress = progressInfo / 100.0;

        // Display the progress to the user
        Progress = normalizedProgress;
    };
};

// Await for the download and install to complete
var result = await operation;

// Check that the download and install was successful
bool installed = result.Status == ExecutionProviderReadyResultState.Success;
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Start the download and install of a NotPresent EP
auto operation = provider.EnsureReadyAsync();

// Listen to progress callback
operation.Progress([this](auto const& asyncInfo, double progressInfo)
{
    // Dispatch to UI thread (varies based on UI platform)
    dispatcherQueue.TryEnqueue([this, progressInfo]()
    {
        // progressInfo is out of 100, convert to 0-1 range
        double normalizedProgress = progressInfo / 100.0;

        // Display the progress to the user
        Progress(normalizedProgress);
    });
});

// Await for the download and install to complete
auto result = operation.get();

// Check that the download and install was successful
bool installed = result.Status() == ExecutionProviderReadyResultState::Success;
```

### [Python](#tab/python)

```python
# Start the download and install of a NotPresent EP
operation = provider.ensure_ready_async()

# Listen to progress callback
def on_progress(async_info, progress_info):
    # progress_info is out of 100, convert to 0-1 range
    normalized_progress = progress_info / 100.0

    # Display the progress to the user
    print(f"Progress: {normalized_progress:.0%}")

operation.progress = on_progress

# Await for the download and install to complete
result = operation.get()

# Check that the download and install was successful
installed = result.status == winml.ExecutionProviderReadyResultState.SUCCESS
```

---

## Next steps

Now that you've installed execution providers, see [Register execution providers](./register-execution-providers.md) to learn how to register them for usage with ONNX Runtime.

## Production app example

For production applications, here's an example of what your app might want to do to give yourself and your users control over when downloads occur. You can check if new execution providers are available and conditionally download them before registering:

### [C#](#tab/csharp)

```csharp
using Microsoft.Windows.AI.MachineLearning;

var catalog = ExecutionProviderCatalog.GetDefault();

// Filter to the EPs our app supports/uses
var providers = catalog.FindAllProviders().Where(p =>
    p.Name == "MIGraphXExecutionProvider" ||
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

---

## See also

* [Register execution providers](./register-execution-providers.md)
* [Supported execution providers and release history](./supported-execution-providers.md)
* [Update execution providers](./update-execution-providers.md)
* [Check execution provider versions](./versioning.md)
* [Common execution provider download issues](./execution-provider-errors.md)