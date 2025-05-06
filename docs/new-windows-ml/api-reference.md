---
title: Windows ML APIs in Microsoft.Windows.AI.MachineLearning
description: Windows Machine Learning (ML), in the `Microsoft.Windows.AI.MachineLearning` namespace, provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.date: 05/06/2025
ms.topic: article
---

# Windows ML APIs in Microsoft.Windows.AI.MachineLearning

For conceptual guidance, see [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md).

You can think of the APIs in the **Microsoft.Windows.AI.MachineLearning** namespace as being the superset of these three sets:
* *New APIs*. Net-new Windows ML APIs, such as the **Infrastructure** class and its methods. Those APIs are documented in the topic you're reading now.
* *APIs from the older version of Windows ML*. Windows ML APIs that were copied over from the **Windows.AI.MachineLearning** namespace. So for the time being you can learn about those APIs&mdash;with the understanding that they exist now also in **Microsoft.Windows.AI.MachineLearning**&mdash;in the documentation for **Windows.AI.MachineLearning**. See [Windows ML APIs in Windows.AI.MachineLearning](../windows-ml/api-reference.md).
* *ONNX Runtime APIs*. Windows ML implementations (in the **Microsoft.Windows.AI.MachineLearning** namespace) of certain APIs from the ONNX Runtime (ORT). For documentation, see the [ONNX Runtime API docs](https://onnxruntime.ai/docs/api/). For example, the [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html). For code examples that use these APIs, and more links to documentation, see the [Use Windows ML to run the ResNet-50 model](./tutorial.md) tutorial.

## Infrastructure class

The **Infrastructure** class provides methods to download and register AI execution providers (EPs) for use with the ONNX Runtime. **Infrastructure** handles the complexity of package management and hardware selection.

This class is the entry point for your app to access hardware-optimized machine learning acceleration through the Windows ML runtime.

```csharp
namespace Microsoft.Windows.AI.MachineLearning
{
    [threading(both)]
    [marshaling_behavior(agile)]
    runtimeclass Infrastructure
    {
        // Default constructor, which initializes an instance of the Infrastructure class.
        Infrastructure();

        // Downloads package dependencies for the current hardware
        Windows.Foundation.IAsyncAction DownloadPackagesAsync();

        // Register execution provider libraries with ORT
        Windows.Foundation.IAsyncOperation RegisterExecutionProviderLibrariesAsync();
    }
}
```

### [C++/WinRT example](#tab/cppwinrt-example-1)

```cppwinrt
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Download the latest execution provider packages.
infrastructure.DownloadPackagesAsync().get();

// Register execution provider libaries with ORT
infrastructure.RegisterExecutionProviderLibrariesAsync().get();

// Call ONNX Runtime (ORT) APIs
Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "MySampleApp");
Ort::SessionOptions session_options(sessionOptions);

// Create path to model, configure session options via ORT, etc.

// Create model session and prepare to run inference
Ort::Session session(env, modelPathToUse.c_str(), session_options);
```

---

### Infrastructure class constructors

#### Default constructor

**Infrastructure.Infrastructure** is the default constructor, which initializes an instance of the **Infrastructure** class.

#### Infrastructure.DownloadPackagesAsync method

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers (EPs) for the device's hardware are installed and up-to-date.

* **DownloadPackagesAsync** downloads the latest compatible version of each EP.

### [C# example](#tab/csharp-example-2)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

try {
    // Download the appropriate packages for the current hardware.
    await infrastructure.DownloadPackagesAsync();
    Console.WriteLine("EP packages downloaded successfully.");
}
catch (Exception ex) {
    Console.WriteLine($"Failed to download EP packages: {ex.Message}.");
}
```

### [C++/WinRT example](#tab/cppwinrt-example-2)

```cppwinrt
try {
    winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;
    
    // Download the appropriate packages for the current hardware.
    infrastructure.DownloadPackagesAsync().get();
    std::wcout << L"EP packages downloaded successfully.\n";
}
catch (winrt::hresult_error const& ex) {
    std::wcout << L"Failed to download EP packages: " << ex.message().c_str() << L".\n";
}
```

### [C# example](#tab/csharp-example-3)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// For Windows ML usage.
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();

// Use with Windows ML session options.
var sessionOptions = new LearningModelSessionOptions();
sessionOptions.ExecutionProviders(executionProviders);

// Create a session with the configured options.
var session = new LearningModelSession(model, device, sessionOptions);
```

### [C++/WinRT ONNX Runtime example](#tab/cppwinrt-example-3)

```cppwinrt
void initialize_windowsml_runtime(OrtSessionOptions* sessionOptions)
{
    // Initialize environment and session.
    const auto& api = Ort::GetApi();
    winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;
    
    auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

    for (auto ep : executionProviders)
    {
        auto onnxRuntimeExecutionProvider = ep.try_as<IOnnxruntimeExecutionProviderNative>();
        if (onnxRuntimeExecutionProvider)
        {
            // Register the execution provider with the ONNX Runtime.
            onnxRuntimeExecutionProvider->Register(&api, sessionOptions);
        }
    }
}
```

---

## Implementation notes

The **Infrastructure** class handles package management internally by using the Microsoft Store **InstallControl** APIs, which must be called from the main Windows ML runtime package, because it's Microsoft-signed. This includes:

* Resolving available execution providers (EPs) for the current hardware configuration.
* Managing package lifetime and updates.
* Handling package registration and activation.

## Package discovery

Execution providers (EPs) are packaged as separate MSIX components that declare the `com.microsoft.windowsmlruntime.executionprovider` extension in their package manifests. This design allows EPs to be updated independently from the Windows ML runtime components.

The Windows ML runtime discovers these packages through the Package Extension infrastructure that was introduced in Windows 11. For each discovered EP, the runtime evaluates hardware compatibility, and loads the appropriate implementation for the current system.

## See also

* [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md)
