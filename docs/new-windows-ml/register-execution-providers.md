---
title: Register execution providers with Windows ML
description: Learn how to register installed AI execution providers with ONNX Runtime using Windows Machine Learning (ML) for hardware-optimized inference.
ms.date: 02/13/2026
ms.topic: how-to
---

# Register execution providers with Windows ML

After execution providers (EPs) are [installed on the device](./initialize-execution-providers.md), you need to register them before they can be used with ONNX Runtime. This page covers the registration APIs and patterns.

## Check registered EPs

By default, only the [included execution providers](./supported-execution-providers.md#included-execution-providers) are present in ONNX Runtime. You can see what EP devices are available in ONNX Runtime by calling ONNX Runtime's `GetEpDevices()` API.

### [C#](#tab/csharp)

```csharp
// Get all ONNX Runtime EP devices
IReadOnlyList<OrtEpDevice> ortEpDevices = OrtEnv.Instance().GetEpDevices();

foreach (var ortEpDevice in ortEpDevices)
{
    Console.WriteLine($"{ortEpDevice.EpName} (DeviceType: {ortEpDevice.HardwareDevice.Type})");
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Get all ONNX Runtime EP devices
Ort::Env env;
auto ortEpDevices = env.GetEpDevices();

for (const auto& ortEpDevice : ortEpDevices)
{
    std::wcout << ortEpDevice.EpName() << L" (DeviceType: " << ortEpDevice.HardwareDevice().Type() << L")" << std::endl;
}
```

### [Python](#tab/python)

```python
import onnxruntime as ort

# Get all ONNX Runtime EP devices
ort_ep_devices = ort.get_ep_devices()

for ort_ep_device in ort_ep_devices:
    print(f"{ort_ep_device.ep_name} (DeviceType: {ort_ep_device.hardware_device.type})")
```

---

Before registering any of the Windows ML execution providers, this code will output the following:

```output
CPUExecutionProvider (DeviceType: CPU)
DmlExecutionProvider (DeviceType: GPU)
```

## Register all installed EPs

To register all available Windows ML execution providers that are currently installed on the machine for use with ONNX Runtime, use the `RegisterCertifiedAsync()` API.

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();

// Register providers already present on the machine
await catalog.RegisterCertifiedAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Register providers already present on the machine
catalog.RegisterCertifiedAsync().get();
```

### [Python](#tab/python)

```python
# Please DO NOT use this API. It won't register EPs to the python ort env.
# Instead, register providers individually as shown below.
```

---

After running `RegisterCertifiedAsync()` on a compatible Qualcomm device that already has the QNN execution provider installed, ONNX Runtime's `GetEpDevices()` will now include two additional EP devices, and the earlier code will output the following...

```output
CPUExecutionProvider (DeviceType: CPU)
DmlExecutionProvider (DeviceType: GPU)
QNNExecutionProvider (DeviceType: NPU)
QNNExecutionProvider (DeviceType: GPU)
```

## Get all installed EPs

To discover which execution providers are currently installed on the user's device, you can check that the **ReadyState** of the execution provider is not `NotPresent`.

### [C#](#tab/csharp)

```csharp
// Get all installed execution providers
IEnumerable<ExecutionProvider> installedProviders = ExecutionProviderCatalog
    .GetDefault()
    .FindAllProviders()
    .Where(i => i.ReadyState != ExecutionProviderReadyState.NotPresent);
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = ExecutionProviderCatalog::GetDefault();

// Get all installed execution providers
auto allProviders = catalog.FindAllProviders();
std::vector<ExecutionProvider> installedProviders;
for (auto const& p : allProviders)
{
    if (p.ReadyState() != ExecutionProviderReadyState::NotPresent)
    {
        installedProviders.push_back(p);
    }
}
```

### [Python](#tab/python)

```python
catalog = winml.ExecutionProviderCatalog.get_default()

# Get all installed execution providers
installed_providers = [
    p for p in catalog.find_all_providers()
    if p.ready_state != winml.ExecutionProviderReadyState.NOT_PRESENT
]
```

---

[!INCLUDE [Explanation of EP ReadyState values](./includes/ep-ready-states.md)]

## Register an installed provider

If there is a specific execution provider your app wants to use, you can register just that provider without registering all present EPs. You first must call `EnsureReadyAsync()` to add the provider to your app's dependency graph. Then, use `TryRegister()` to register the EP with ONNX Runtime.

### [C#](#tab/csharp)

```csharp
// Add the provider to the app's dependency graph if needed
var result = await installedProvider.EnsureReadyAsync();

// If adding to the app's dependency graph succeeded
if (result.Status == ExecutionProviderReadyResultState.Success)
{
    // Register it with ONNX Runtime
    bool registered = installedProvider.TryRegister();
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
// Add the provider to the app's dependency graph if needed
auto result = installedProvider.EnsureReadyAsync().get();

// If adding to the app's dependency graph succeeded
if (result.Status() == ExecutionProviderReadyResultState::Success)
{
    // Register it with ONNX Runtime
    bool registered = installedProvider.TryRegister();
}
```

### [Python](#tab/python)

```python
# Add the provider to the app's dependency graph if needed
result = installed_provider.ensure_ready_async().get()

# If adding to the app's dependency graph succeeded
if result.status == winml.ExecutionProviderReadyResultState.SUCCESS:
    # Register it with ONNX Runtime
    ort.register_execution_provider_library(installed_provider.name, installed_provider.library_path)
```

---

## Next steps

Now that you've registered execution providers for use with ONNX Runtime, see [Select execution providers in ONNX Runtime](./select-execution-providers.md) to learn how to use those execution providers in ONNX Runtime.

## See also

* [Install execution providers](./initialize-execution-providers.md)
* [Select execution providers in ONNX Runtime](./select-execution-providers.md)
* [Supported execution providers and release history](./supported-execution-providers.md)
* [Update execution providers](./update-execution-providers.md)
* [Check execution provider versions](./versioning.md)
* [Common execution provider download issues](./execution-provider-errors.md)
