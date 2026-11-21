---
title: Update execution providers in Windows ML
description: Learn how Windows ML updates execution providers automatically and how to update them or require a minimum version from your app.
author: GrantMeStrength
ms.author: jken
ms.date: 11/20/2026
ms.topic: how-to
---

# Update execution providers in Windows ML

After an execution provider (EP) is installed (as seen in [Install Windows ML EPs](./initialize-execution-providers.md)), Windows ML automatically updates it in the background. These compatible updates become available to your app without any app changes, so your app benefits from performance improvements and support for new operators.

Background updates can reach a device later than you need. If your app wants the newest EP now, or depends on a fix in a specific EP version, you can update the EP programmatically.

> [!NOTE]
> The programmatic update APIs require Windows ML version 2.9.42-experimental or later.

To see the release history of EPs, see [Windows ML execution providers releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases). To check what version of an EP a device has, see [Check Windows ML EP versions](./versioning.md).

## Choose an update approach

`ExecutionProvider.EnsureReadyAsync()` and `ExecutionProviderCatalog.EnsureAndRegisterCertifiedAsync()` download an EP only if it isn't installed or the installed version is below the minimum in the Windows ML catalog. Otherwise they load the installed version without checking for a newer one. The following APIs add control over updates:

| API | Use it when | Behavior |
|-----|-------------|----------|
| `ExecutionProvider.TryUpdateAndEnsureReadyAsync()` | You want the newest version but can run on the installed one. | Best effort. If the update fails but the installed version can be made ready, the result status is `SuccessWithoutUpdate`, and `ExtendedError` gives the reason. |
| `ExecutionProvider.EnsureReadyAsync(PackageVersion minimumVersion)` | Your app requires a specific version or later. | Required. If the installed version meets the minimum, it's used without checking for updates. If no version at or above the minimum can be made ready, the call fails with `ERROR_PRODUCT_VERSION`. |
| `ExecutionProviderCatalog.UpdateAndRegisterCertifiedAsync()` | You want to update and register all certified EPs. | Best effort. An EP whose update fails is registered at its installed version. |

Like `EnsureReadyAsync()`, the per-provider methods don't register the EP with ONNX Runtime. Call `TryRegister()` afterward. Progress and completion handlers run on a thread-pool thread.

The new `ExecutionProviderReadyResultState.SuccessWithoutUpdate` value means the EP is ready at its installed version but wasn't updated.

## Update an EP when possible

The following example updates an EP at startup but keeps working offline by using the installed version.

### [C#](#tab/csharp)

```csharp
using Microsoft.Windows.AI.MachineLearning;

ExecutionProvider? provider = ExecutionProviderCatalog.GetDefault().FindAllProviders()
    .FirstOrDefault(p => p.Name == "QNNExecutionProvider");

if (provider != null)
{
    ExecutionProviderReadyResult result = await provider.TryUpdateAndEnsureReadyAsync();
    if (result.Status == ExecutionProviderReadyResultState.SuccessWithoutUpdate)
    {
        // The EP runs on the installed version, for example because the device is offline.
        Console.WriteLine($"EP not updated: 0x{result.ExtendedError.HResult:X8}");
    }

    if (result.Status != ExecutionProviderReadyResultState.Failure)
    {
        provider.TryRegister();
    }
}
```

### [C++/WinRT](#tab/cppwinrt)

```cpp
using namespace winrt::Microsoft::Windows::AI::MachineLearning;

winrt::Windows::Foundation::IAsyncAction PrepareProviderAsync(ExecutionProvider provider)
{
    auto operation = provider.TryUpdateAndEnsureReadyAsync();
    operation.Progress([](auto const&, double progress) { /* progress is 0-100 */ });

    ExecutionProviderReadyResult result = co_await operation;
    if (result.Status() == ExecutionProviderReadyResultState::Failure)
    {
        co_return; // No version of the EP could be made ready.
    }

    // Success, or SuccessWithoutUpdate with the reason in ExtendedError().
    provider.TryRegister();
}
```

---

## Require a minimum version

The following example requires version 1.8.15 or later before the app creates an inference session. If the installed version is already new enough, nothing is downloaded.

### [C#](#tab/csharp)

```csharp
using Microsoft.Windows.AI.MachineLearning;
using Windows.ApplicationModel;

var minimumVersion = new PackageVersion { Major = 1, Minor = 8, Build = 15, Revision = 0 };
ExecutionProviderReadyResult result = await provider.EnsureReadyAsync(minimumVersion);

if (result.Status == ExecutionProviderReadyResultState.Success)
{
    provider.TryRegister();
}
else if (result.ExtendedError.HResult == unchecked((int)0x80070666)) // ERROR_PRODUCT_VERSION
{
    // The required version isn't available on this device yet.
}
```

### [C++/WinRT](#tab/cppwinrt)

```cpp
winrt::Windows::ApplicationModel::PackageVersion minimumVersion{ 1, 8, 15, 0 };
ExecutionProviderReadyResult result = co_await provider.EnsureReadyAsync(minimumVersion);

if (result.Status() == ExecutionProviderReadyResultState::Success)
{
    provider.TryRegister();
}
else if (result.ExtendedError() == HRESULT_FROM_WIN32(ERROR_PRODUCT_VERSION))
{
    // The required version isn't available on this device yet.
}
```

---

## Update all certified EPs

`UpdateAndRegisterCertifiedAsync()` updates every certified EP it can and registers them with ONNX Runtime. If the device is offline, it registers the installed versions.

```csharp
IList<ExecutionProvider> registered =
    await ExecutionProviderCatalog.GetDefault().UpdateAndRegisterCertifiedAsync();
```

The method returns the EPs it registered, but the list doesn't indicate which were updated. An EP that isn't installed and can't be acquired isn't registered. Progress is reported as a fraction from 0 to 1, as with `EnsureAndRegisterCertifiedAsync()`. The per-provider methods report progress from 0 to 100.

## Interpret results

The WinRT methods report failures through `ExecutionProviderReadyResult` instead of throwing.

| Method | Status | ExtendedError | Meaning |
|--------|--------|---------------|---------|
| `TryUpdateAndEnsureReadyAsync` | `Success` | `S_OK` | Ready. The EP was updated, is already current, or isn't an EP that Windows ML can update. |
| `TryUpdateAndEnsureReadyAsync` | `SuccessWithoutUpdate` | Update error | Ready at the installed version, but the update failed, for example because the device is offline. |
| `TryUpdateAndEnsureReadyAsync` | `SuccessWithoutUpdate` | `E_ILLEGAL_STATE_CHANGE` | The EP is already loaded in this process, so no update was checked for. This doesn't mean a newer version exists. |
| `TryUpdateAndEnsureReadyAsync` | `Failure` | Error | No version could be made ready, for example an EP that isn't installed couldn't be acquired. |
| `EnsureReadyAsync(minimumVersion)` | `Success` | `S_OK` | Ready at or above the minimum. |
| `EnsureReadyAsync(minimumVersion)` | `Failure` | `ERROR_PRODUCT_VERSION` | No version at or above the minimum can be made ready, including an EP already loaded at a lower version. |
| `EnsureReadyAsync(minimumVersion)` | `Failure` | Error | Acquisition or load failure. |

## Considerations

### EPs already loaded in the process

Once an EP is loaded, its binaries are pinned and can't be replaced in the running process. `TryUpdateAndEnsureReadyAsync()` keeps the loaded EP and reports `SuccessWithoutUpdate` with `E_ILLEGAL_STATE_CHANGE`. `EnsureReadyAsync(minimumVersion)` succeeds if the loaded version meets the minimum, and otherwise fails with `ERROR_PRODUCT_VERSION`.

Call these methods before the EP is loaded, for example at app startup, or restart the process to use a newer version. Other processes keep the version they loaded.

### EPs that Windows ML doesn't distribute

Windows ML can update only EPs that it distributes. For other EPs, `TryUpdateAndEnsureReadyAsync()` makes the available version ready and reports `Success`:

| EP | Version made ready |
|----|--------------------|
| Not in the Windows ML catalog, for example a sideloaded package | The installed version. |
| Serviced by Windows (pre-staged on the device) | The newest version already on the device. Nothing is downloaded. |

To require a specific version of such an EP, use `EnsureReadyAsync(minimumVersion)`. An EP included in the Windows ML package has no package version, so only a minimum version of 0.0.0.0 is met.

## Use the Flat-C API

Native apps that use the Windows ML Flat-C API (`WinMLEpCatalog.h`) can use the following functions. Each has a blocking and an async form, matching `WinMLEpEnsureReady` and `WinMLEpEnsureReadyAsync`.

* Best effort: `WinMLEpTryUpdateAndEnsureReady`, `WinMLEpTryUpdateAndEnsureReadyAsync`, and `WinMLEpTryUpdateAndEnsureReadyGetResult`
* Minimum version: `WinMLEpEnsureReadyWithMinimumVersion` and `WinMLEpEnsureReadyWithMinimumVersionAsync`

`WinMLEpTryUpdateAndEnsureReady` returns `S_OK` whenever the EP is ready and reports the update result through an `updateResult` output. The async form reports it through `WinMLEpTryUpdateAndEnsureReadyGetResult`. The minimum-version functions return the same codes as `EnsureReadyAsync(minimumVersion)`.

`WinMLEpVersion` maps to the first three parts of the MSIX package version, and the revision is treated as 0. Each part must be from 0 to 65535, or the call fails with `E_INVALIDARG`.

```c
WinMLAsyncBlock async = { 0 };
HRESULT hr = WinMLEpTryUpdateAndEnsureReadyAsync(ep, &async);
if (SUCCEEDED(hr))
{
    hr = WinMLAsyncGetStatus(&async, TRUE /* wait */);
    if (SUCCEEDED(hr))
    {
        HRESULT updateResult = S_OK;
        WinMLEpTryUpdateAndEnsureReadyGetResult(&async, &updateResult);
        // updateResult is S_OK, or the reason the update wasn't applied.
    }

    WinMLAsyncClose(&async);
}
```

The blocking forms return the result directly. Don't call them on a UI thread, because they can wait for a download.

```c
WinMLEpVersion minimumVersion = { 1, 8, 15 };
HRESULT hr = WinMLEpEnsureReadyWithMinimumVersion(ep, &minimumVersion);
if (hr == HRESULT_FROM_WIN32(ERROR_PRODUCT_VERSION))
{
    // The required version isn't available on this device.
}
```

## See also

* [Windows ML EPs and release history](./supported-execution-providers.md)
* [Install Windows ML EPs](./initialize-execution-providers.md)
* [Check Windows ML EP versions](./versioning.md)
* [Common execution provider download issues](./execution-provider-errors.md)
