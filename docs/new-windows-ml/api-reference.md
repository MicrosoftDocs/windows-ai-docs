---
title: Windows ML APIs
description: Learn about the APIs behind Windows Machine Learning (ML) which help your Windows apps run AI models locally.
ms.date: 07/07/2025
ms.topic: article
---

# Windows ML APIs

For conceptual guidance, see [Run ONNX models with Windows ML)](./run-onnx-models.md).

You can think of the APIs in the *Microsoft.WindowsAppSDK.ML* NuGet package as being the superset of these two sets:

* *Windows ML APIs*. Windows ML APIs in the **Microsoft.Windows.AI.MachineLearning** namespace, such as the **ExecutionProviderCatalog** class and its methods (which are Windows Runtime APIs). These APIs are documented in the topic you're reading now.
* *ONNX Runtime APIs*. Windows ML implementations (in the *Microsoft.WindowsAppSDK.ML* NuGet package) of certain APIs from the ONNX Runtime (ORT). For documentation, see the [ONNX Runtime API docs](https://onnxruntime.ai/docs/api/). For example, the [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html). For code examples that use these APIs, and more links to documentation, see the [Use Windows ML to run the ResNet-50 model](./tutorial.md) tutorial.

## The Microsoft.WindowsAppSDK.ML NuGet package

The Microsoft Windows ML runtime provides APIs for machine learning and AI operations in Windows applications. The *Microsoft.WindowsAppSDK.ML* NuGet package provides the Windows ML runtime `.winmd` files for use in both C# and C++ projects.

## The pywinrt Python wheels

The Microsoft Windows ML runtime leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project to provide Python access to the same Windows ML APIs. The package name is *winui3-Microsoft.Windows.AI.MachineLearning*. Additional packages are required to use Windows App SDK in python. For details, see the [Run ONNX models with Windows ML](./run-onnx-models.md) topic.

## Windows ML APIs

### ExecutionProviderCatalog class

The **ExecutionProviderCatalog** class provides methods to discover, acquire, and register AI execution providers (EPs) for use with the ONNX Runtime. It handles the complexity of package management and hardware selection.

This class is the entry point for your app to access hardware-optimized machine learning acceleration through the Windows ML runtime.

#### [C#](#tab/csharp)

```csharp
// Get the default catalog
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();

// Ensure and register all compatible execution providers
await catalog.EnsureAndRegisterAllAsync();

// Use ONNX Runtime directly for inference (using Microsoft.ML.OnnxRuntime namespace)
```

#### [C++/WinRT](#tab/cpp)

```cppwinrt
// Get the default catalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog = 
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers
catalog.EnsureAndRegisterAllAsync().get();

// Use ONNX Runtime C API directly for inference
```

#### [Python](#tab/Python)

```Python
import winui3.microsoft.windows.ai.machinelearning as winml
# Get the default catalog
catalog = winml.ExecutionProviderCatalog.get_default()
# DO NOT call EnsureAndRegisterAllAsync() in Python. That will not work for the onnxruntime Python environment.
# Instead, register execution providers following this pattern:
providers = catalog.find_all_providers()
    for provider in providers:
        provider.ensure_ready_async().get()
        ort.register_execution_provider_library(provider.name, provider.library_path)
```

---

#### ExecutionProviderCatalog methods

##### ExecutionProviderCatalog.GetDefault method

Returns the default ExecutionProviderCatalog instance that provides access to all execution providers on the system.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
```

##### [Python](#tab/Python)

```Python
catalog = winml.ExecutionProviderCatalog.get_default()
```

---

##### ExecutionProviderCatalog.FindAllProviders method

Returns a collection of all execution providers compatible with the current hardware.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
var providers = catalog.FindAllProviders();

foreach (var provider in providers)
{
    Console.WriteLine($"Found provider: {provider.Name}, Type: {provider.DeviceType}");
}
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

for (const auto& provider : providers)
{
    std::wcout << L"Found provider: " << provider.Name().c_str() 
              << L", Type: " << static_cast<int>(provider.DeviceType()) << L"\n";
}
```

##### [Python](#tab/Python)

```Python
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()
for provider in providers:
    print(f"Found provider: {provider.name}, Type: {provider.device_type}")
```

---

##### ExecutionProviderCatalog.EnsureAndRegisterAllAsync method

Ensures all compatible execution providers are ready and registers them with ONNX Runtime.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();

try
{
    // This will ensure providers are ready and register them with ONNX Runtime
    await catalog.EnsureAndRegisterAllAsync();
    Console.WriteLine("All execution providers are ready and registered");
}
catch (Exception ex)
{
    Console.WriteLine($"Failed to prepare execution providers: {ex.Message}");
}
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

try 
{
    // This will ensure providers are ready and register them with ONNX Runtime
    catalog.EnsureAndRegisterAllAsync().get();
    std::wcout << L"All execution providers are ready and registered\n";
}
catch (const winrt::hresult_error& ex) 
{
    std::wcout << L"Failed to prepare execution providers: " << ex.message().c_str() << L"\n";
}
```

##### [Python](#tab/Python)

```Python
# DO NOT call EnsureAndRegisterAllAsync() in Python. 
# Onnxruntime's Python and native environments are separate.
# This method will not work for the onnxruntime Python environment.
```

---

##### ExecutionProviderCatalog.RegisterAllAsync method

Registers all compatible execution providers with ONNX Runtime without ensuring they are ready. This only registers providers that are already present on the machine, avoiding potentially long download times which may be required by `EnsureAndRegisterAllAsync`.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
await catalog.RegisterAllAsync();
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
catalog.RegisterAllAsync().get();
```

##### [Python](#tab/Python)

```Python
# DO NOT call RegisterAllAsync() in Python.
```

---

### ExecutionProvider class

The ExecutionProvider class represents a specific hardware accelerator that can be used for machine learning inference.

#### ExecutionProvider methods

##### ExecutionProvider.EnsureReadyAsync method

Ensures the execution provider is ready for use by downloading and installing any required components.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
var providers = catalog.FindAllProviders();

foreach (var provider in providers)
{
    await provider.EnsureReadyAsync();
    Console.WriteLine($"Provider {provider.Name} is ready");
}
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

for (const auto& provider : providers)
{
    provider.EnsureReadyAsync().get();
    std::wcout << L"Provider " << provider.Name().c_str() << L" is ready\n";
}
```

##### [Python](#tab/Python)

```Python
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()
for provider in providers:
    provider.ensure_ready_async().get()
    print(f"Provider {provider.name} is ready")
```

---

##### ExecutionProvider.TryRegister method

Attempts to register the execution provider with ONNX Runtime and returns a boolean indicating success.

#### [C#](#tab/csharp)

```csharp
var catalog = Microsoft.Windows.AI.MachineLearning.ExecutionProviderCatalog.GetDefault();
var providers = catalog.FindAllProviders();

foreach (var provider in providers)
{
    await provider.EnsureReadyAsync();
    bool registered = provider.TryRegister();
    Console.WriteLine($"Provider {provider.Name} registration: {(registered ? "Success" : "Failed")}");
}
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

for (const auto& provider : providers)
{
    provider.EnsureReadyAsync().get();
    bool registered = provider.TryRegister();
    std::wcout << L"Provider " << provider.Name().c_str() 
              << L" registration: " << (registered ? L"Success" : L"Failed") << L"\n";
}
```

##### [Python](#tab/Python)

```Python
catalog = winml.ExecutionProviderCatalog.get_default()
providers = catalog.find_all_providers()
for provider in providers:
    provider.ensure_ready_async().get()
    # DO NOT call TryRegister() in Python.
    # Use the name and library_path to directly register with onnxruntime.
    ort.register_execution_provider_library(provider.name, provider.library_path)
    print(f"Registered execution provider: {provider.name}")
```

---

#### ExecutionProvider properties

| Name | Type | Description |
|-|-|-|
| Name | string | Gets the name of the execution provider |
| DeviceType | ExecutionProviderDeviceType | Gets the type of device (CPU, GPU, NPU, etc.) |
| IsReady | bool | Gets whether the execution provider is ready for use |
| LibraryPath | string | Gets the path to the execution provider library |



## Implementation notes

The Windows ML runtime is integrated with the Windows App SDK and relies on its deployment and bootstrapping mechanisms:

* Automatically discovers execution providers compatible with current hardware
* Manages package lifetime and updates
* Handles package registration and activation
* Supports different versions of execution providers

#### Framework-Dependent Deployment

Windows ML is delivered as a _framework-dependent_ component. This means your app must either:

* Reference the main Windows App SDK NuGet package by adding a reference to `Microsoft.WindowsAppSDK` (recommended)
* Or, reference both `Microsoft.WindowsAppSDK.ML` and `Microsoft.WindowsAppSDK.Runtime`

For more information on deploying Windows App SDK applications, see the [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview) documentation.

#### Using ONNX Runtime with Windows ML

For C++ applications, after registering execution providers, use the ONNX Runtime C API directly to create sessions and run inference.

For C# applications, use the ONNX Runtime directly for inference using the `Microsoft.ML.OnnxRuntime` namespace.

For Python applications, use the separate ONNX Runtime wheel (`onnxruntime`) for inference. For the experimental release, please use the `onnxruntime-winml==1.22.0.post2` package from index `https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple`.

#### Python notes

**Initialize Windows App SDK**

All Windows ML calls should happen after the Windows App SDK is initialized. This can be done with the following code:

```python
from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
    InitializeOptions,
    initialize
)
with initialize(options = InitializeOptions.ON_NO_MATCH_SHOW_UI):
    # Your Windows ML code here
```

**Registration happens out of WinML**

The ONNX runtime is designed in a way where the Python and native environments are separate. And native registration calls in the same process will not work for the Python environment. Thus, the registration of execution providers should be done with the Python API directly.

**Use pywinrt in another process**

Due to some limitations in the Python projection of WinRT, it's recommended to get the execution provider information in a separate worker process. For a complete example, see the [Windows ML Python sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML/Python).

## See also

* [Run ONNX models with Windows ML](./run-onnx-models.md)
* [Windows App SDK Documentation](/windows/apps/windows-app-sdk/)
* [Windows App SDK on GitHub](https://github.com/microsoft/WindowsAppSDK)
* [Windows ML Samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)
* [ONNX Runtime API Documentation](https://onnxruntime.ai/docs/api/)
* [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview)
