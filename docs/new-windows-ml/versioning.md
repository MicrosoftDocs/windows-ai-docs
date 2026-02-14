---
title: Check execution provider versions in Windows ML
description: Learn how to check which version of an execution provider is currently installed in Windows ML.
ms.date: 01/06/2026
ms.topic: how-to
---

# Check execution provider versions in Windows ML

Most execution providers in Windows ML are dynamically acquired via Windows Update at runtime as seen in [install execution providers](./initialize-execution-providers.md), and updated versions are automatically updated (with compatible updates) as described in [update execution providers](./update-execution-providers.md), meaning the version of the EP might vary over time.

See the [supported execution providers docs](./supported-execution-providers.md) to see what execution providers are available and their release history.

## Check your end-user's EP version

You can programmatically check the version of an execution provider (EP) that's present on the device by inspecting the [**PackageId**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning.executionprovider.packageid) property on [**ExecutionProvider**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning.executionprovider).

If the EP is not yet present, PackageId will return null.

### [C#](#tab/csharp)

```csharp
// Get all EPs compatible with this device
var providers = ExecutionProviderCatalog.GetDefault().FindAllProviders();

// For each provider
foreach (var provider in providers)
{
    // Log the name
    Debug.WriteLine($"Windows ML EP: {provider.Name}");

    // Log the version
    if (provider.PackageId != null)
    {
        var v = provider.PackageId.Version;
        Debug.WriteLine($"Version: {v.Major}.{v.Minor}.{v.Build}.{v.Revision}");
    }
    else
    {
        Debug.WriteLine("Version: Not installed");
    }
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

// For each provider
for (auto const& provider : providers)
{
    // Log the name
    OutputDebugString((L"Windows ML EP: " + provider.Name() + L"\n").c_str());

    // Log the version
    auto packageId = provider.PackageId();
    if (packageId)
    {
        auto v = packageId.Version();
        std::wstring versionStr = std::format(L"Version: {}.{}.{}.{}\n",
            v.Major, v.Minor, v.Build, v.Revision);
        OutputDebugString(versionStr.c_str());
    }
    else
    {
        OutputDebugString(L"Version: Not installed\n");
    }
}
```

### [Python](#tab/python)

```python
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()

# For each provider
for provider in providers:
    # Log the name
    print(f"Windows ML EP: {provider.name}")

    # Log the version
    if provider.package_id is not None:
        v = provider.package_id.version
        print(f"Version: {v.major}.{v.minor}.{v.build}.{v.revision}")
    else:
        print("Version: Not installed")
```

---

On a device with the QNN EP installed, this code outputs the following...

```
Windows ML EP: QNNExecutionProvider
Version: 1.8.27.0
```

## Check your own device's EP version

You can also easily check which version of an EP is installed on your development device by using PowerShell.

```powershell
Get-AppxPackage MicrosoftCorporationII.WinML.*
```

On a device with the QNN EP installed, this outputs the following...

```powershell
Name              : MicrosoftCorporationII.WinML.Qualcomm.QNN.EP.1.8
Publisher         : CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US
Architecture      : Arm64
ResourceId        :
Version           : 1.8.27.0
...
```

## See also

* [Supported execution providers and release history](./supported-execution-providers.md)
* [Install execution providers](./initialize-execution-providers.md)
* [Update execution providers](./update-execution-providers.md)
* [Common execution provider download issues](./execution-provider-errors.md)