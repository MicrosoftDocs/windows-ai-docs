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

## Python support

Windows ML can be used from Python applications through the Python projection of the Windows Runtime (WinRT) APIs. This enables Python developers to leverage hardware acceleration for machine learning workloads on Windows devices.

### Prerequisites

To use Windows ML in Python:

1. Install Python 3.8 or later
2. Install the Windows App SDK
3. Install the required Python packages:
   ```
   pip install winrt onnxruntime pillow numpy
   ```

### Usage pattern

To use Windows ML in Python applications:

```python
# Import required modules
from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
    InitializeOptions,
    initialize
)
import winui3.microsoft.windows.ai.machinelearning as winml
import onnxruntime as ort
import json

# Initialize Windows App SDK
with initialize(options=InitializeOptions.ON_NO_MATCH_SHOW_UI):
    pass

# Get execution provider paths
def get_execution_provider_paths():
    eps = {}
    catalog = winml.ExecutionProviderCatalog.get_default()
    providers = catalog.find_all_providers()
    
    for provider in providers:
        provider.ensure_ready_async().get()
        eps[provider.name] = provider.library_path
    
    return eps

# Register execution providers with ONNX Runtime
ep_paths = get_execution_provider_paths()
for name, path in ep_paths.items():
    ort.register_execution_provider_library(name, path)
    print(f"Registered execution provider: {name} with library path: {path}")

# Continue with ONNX Runtime for inference
```

Due to some limitations in the Python projection of WinRT, it's recommended to handle the execution provider registration in a separate worker process. For a complete example, see the [Windows ML Python sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML/python).

## See also

* [Run ONNX models with Windows ML](./run-onnx-models.md)
* [Windows App SDK Documentation](/windows/apps/windows-app-sdk/)
* [Windows App SDK on GitHub](https://github.com/microsoft/WindowsAppSDK)
* [Windows ML Samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)
* [ONNX Runtime API Documentation](https://onnxruntime.ai/docs/api/)
* [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview)
