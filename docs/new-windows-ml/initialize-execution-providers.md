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

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure execution providers compatible with device are present (downloads if necessary)
// and then registers all present execution providers with ONNX Runtime
catalog.EnsureAndRegisterCertifiedAsync().get();
```

### [C/C++](#tab/c)

The C APIs do not support a single `EnsureAndRegisterCertifiedAsync()` call. Instead, follow the instructions further down to download and register a specific EP.

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

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
auto catalog = ExecutionProviderCatalog::GetDefault();

// Find all available EPs (including non-installed EPs)
auto providers = catalog.FindAllProviders();

for (auto const& provider : providers)
{
    std::wcout << provider.Name() << L": " << static_cast<int>(provider.ReadyState()) << std::endl;
}
```

### [C/C++](#tab/c)

```cpp
#include <WinMLEpCatalog.h>

// Context structure to track enumeration state
struct EnumContext
{
    bool needsDownload;
};

// Callback to check if any providers need downloading
BOOL CALLBACK CheckDownloadNeededCallback(
    WinMLEpHandle ep,
    const WinMLEpInfo* info,
    void* context)
{
    if (info == nullptr) return TRUE; // Skip invalid entries
    EnumContext* ctx = static_cast<EnumContext*>(context);
    if (info->readyState == WinMLEpReadyState_NotPresent)
    {
        ctx->needsDownload = true;
        return FALSE; // Stop enumeration early
    }
    return TRUE; // Continue enumeration
}

void DiscoverProviders()
{
    WinMLEpCatalogHandle catalog = nullptr;
    HRESULT hr = WinMLEpCatalogCreate(&catalog);
    if (FAILED(hr)) return;

    EnumContext ctx = { false };

    // Check if any providers need to be downloaded
    WinMLEpCatalogEnumProviders(catalog, CheckDownloadNeededCallback, &ctx);

    if (ctx.needsDownload)
    {
        // There are new EPs available; decide how your app wants to handle that.
        // See "Install a specific EP" below for download and registration.
    }

    WinMLEpCatalogRelease(catalog);
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

You'll first use `FindAllProviders()` to get all compatible EPs, and then you can call `EnsureReadyAsync()` on a particular **ExecutionProvider** to download the specific execution provider, and call `TryRegister()` to register the specific execution provider.

```csharp
// Download and install a NotPresent EP
var result = await provider.EnsureReadyAsync();

// Check that the download and install was successful
bool installed = result.Status == ExecutionProviderReadyResultState.Success;
```

### [C++/WinRT](#tab/cppwinrt)

You'll first use `FindAllProviders()` to get all compatible EPs, and then you can call `EnsureReadyAsync()` on a particular **ExecutionProvider** to download the specific execution provider, and call `TryRegister()` to register the specific execution provider.

```cppwinrt
// Download and install a NotPresent EP
auto result = provider.EnsureReadyAsync().get();

// Check that the download and install was successful
bool installed = result.Status() == ExecutionProviderReadyResultState::Success;
```

### [C/C++](#tab/c)

You'll use the `WinMLEpCatalogFindProvider` method to ask for a specific execution provider, and then you can call `WinMLEpEnsureReady` passing in the **WinMLEpHandle** to download the specific execution provider, and then use `WinMLEpGetLibraryPathSize` and `WinMLEpGetLibraryPath` to get the path to the execution provider and register it with ONNX Runtime using `RegisterExecutionProviderLibrary`.

```cpp
#include <WinMLEpCatalog.h>
#include <onnxruntime_cxx_api.h>
#include <filesystem>
#include <string>

// Assumes an Ort::Env has already been created
// Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "MyApp");

WinMLEpCatalogHandle catalog = nullptr;
HRESULT hr = WinMLEpCatalogCreate(&catalog);
if (FAILED(hr)) return;

// Find the QNN provider
WinMLEpHandle qnnProvider = nullptr;
hr = WinMLEpCatalogFindProvider(catalog, "QNN", nullptr, &qnnProvider);

if (SUCCEEDED(hr) && qnnProvider != nullptr)
{
    // Ensure it's ready (download if necessary)
    hr = WinMLEpEnsureReady(qnnProvider);

    if (SUCCEEDED(hr))
    {
        // Get the library path for registration
        size_t pathSize = 0;
        WinMLEpGetLibraryPathSize(qnnProvider, &pathSize);

        std::string libraryPathUtf8(pathSize, '\0');
        WinMLEpGetLibraryPath(qnnProvider, pathSize, libraryPathUtf8.data(), nullptr);

        // Register with ONNX Runtime
        std::filesystem::path libraryPath(libraryPathUtf8);
        env.RegisterExecutionProviderLibrary("QNN", libraryPath.wstring());
    }
}

WinMLEpCatalogRelease(catalog);
```

### [Python](#tab/python)

You'll first use `find_all_providers()` to get all compatible EPs, and then you can call `ensure_ready_async()` on a particular **ExecutionProvider** to download the specific execution provider, and use ONNX Runtime's `register_execution_provider_library` to register the specific execution provider.

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

### [C++/WinRT](#tab/cppwinrt)

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

### [C/C++](#tab/c)

```cpp
#include <WinMLEpCatalog.h>
#include <WinMLAsync.h>
#include <iostream>
#include <format>

// Progress callback — called periodically during download
void CALLBACK OnProgress(WinMLAsyncBlock* async, double progress)
{
    // progress is out of 100, convert to 0-1 range
    double normalizedProgress = progress / 100.0;

    // Display the progress to the user
    std::cout << std::format("Progress: {:.0f}%\n", normalizedProgress * 100);
}

// Completion callback — called when the download finishes
void CALLBACK OnComplete(WinMLAsyncBlock* async)
{
    HRESULT hr = WinMLAsyncGetStatus(async, FALSE);
    if (SUCCEEDED(hr))
    {
        std::cout << "Download complete!\n";
    }
    else
    {
        std::cout << std::format("Download failed: 0x{:08X}\n", static_cast<uint32_t>(hr));
    }
}

// Start the async download with progress
WinMLAsyncBlock async = {};
async.callback = OnComplete;
async.progress = OnProgress;

HRESULT hr = WinMLEpEnsureReadyAsync(ep, &async);
if (SUCCEEDED(hr))
{
    // Wait for the async operation to complete
    WinMLAsyncGetStatus(&async, TRUE);
}

WinMLAsyncClose(&async);
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

### [C++/WinRT](#tab/cppwinrt)

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

### [C/C++](#tab/c)

```cpp
#include <WinMLEpCatalog.h>
#include <onnxruntime_cxx_api.h>
#include <filesystem>
#include <string>
#include <cstring>

// List of provider names our app supports
const char* targetProviderNames[] = {
    "VitisAIExecutionProvider",
    "OpenVINOExecutionProvider",
    "QNNExecutionProvider",
    "NvTensorRtRtxExecutionProvider"
};
const size_t targetProviderCount = sizeof(targetProviderNames) / sizeof(targetProviderNames[0]);

bool IsTargetProvider(const char* name)
{
    for (size_t i = 0; i < targetProviderCount; i++)
    {
        if (strcmp(name, targetProviderNames[i]) == 0)
            return true;
    }
    return false;
}

// Context for enumeration callbacks
struct ProductionContext
{
    bool needsDownload;
    bool userWantsToDownload;
    Ort::Env* env;
};

// Check if any target providers need downloading
BOOL CALLBACK CheckTargetProvidersCallback(
    WinMLEpHandle ep,
    const WinMLEpInfo* info,
    void* context)
{
    if (info == nullptr || info->name == nullptr) return TRUE; // Skip invalid entries
    ProductionContext* ctx = static_cast<ProductionContext*>(context);
    if (IsTargetProvider(info->name) && info->readyState == WinMLEpReadyState_NotPresent)
    {
        ctx->needsDownload = true;
    }
    return TRUE; // Continue to check all providers
}

// Download missing and register ready target providers
BOOL CALLBACK ProcessTargetProvidersCallback(
    WinMLEpHandle ep,
    const WinMLEpInfo* info,
    void* context)
{
    if (info == nullptr || info->name == nullptr) return TRUE; // Skip invalid entries
    ProductionContext* ctx = static_cast<ProductionContext*>(context);

    if (!IsTargetProvider(info->name))
        return TRUE; // Skip non-target providers

    // Download if user agreed and provider is not present
    if (ctx->userWantsToDownload && info->readyState == WinMLEpReadyState_NotPresent)
    {
        WinMLEpEnsureReady(ep);
    }

    // Re-check state and register if ready
    WinMLEpReadyState currentState;
    WinMLEpGetReadyState(ep, &currentState);
    if (currentState == WinMLEpReadyState_Ready)
    {
        // Get the library path
        size_t pathSize = 0;
        WinMLEpGetLibraryPathSize(ep, &pathSize);
        std::string libraryPathUtf8(pathSize, '\0');
        WinMLEpGetLibraryPath(ep, pathSize, libraryPathUtf8.data(), nullptr);

        // Register with ONNX Runtime
        std::filesystem::path libraryPath(libraryPathUtf8);
        ctx->env->RegisterExecutionProviderLibrary(info->name, libraryPath.wstring());
    }

    return TRUE;
}

void ProductionAppExample(Ort::Env& env, bool userWantsToDownload)
{
    WinMLEpCatalogHandle catalog = nullptr;
    HRESULT hr = WinMLEpCatalogCreate(&catalog);
    if (FAILED(hr)) return;

    ProductionContext ctx = { false, userWantsToDownload, &env };

    // First pass: check if any target providers need downloading
    WinMLEpCatalogEnumProviders(catalog, CheckTargetProvidersCallback, &ctx);

    if (ctx.needsDownload && !userWantsToDownload)
    {
        // TODO: Show UI to user asking if they want to download
        // ctx.userWantsToDownload = ShowDownloadDialog();
    }

    // Second pass: download (if requested) and register target providers
    WinMLEpCatalogEnumProviders(catalog, ProcessTargetProvidersCallback, &ctx);

    WinMLEpCatalogRelease(catalog);
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