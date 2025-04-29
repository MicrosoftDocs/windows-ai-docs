---
title: Windows ML APIs in Microsoft.Windows.AI.MachineLearning
description: Windows Machine Learning (ML), in the `Microsoft.Windows.AI.MachineLearning` namespace, provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.date: 04/29/2025
ms.topic: article
---

# Windows ML APIs in Microsoft.Windows.AI.MachineLearning

For conceptual guidance, see [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md).

## Infrastructure class

The **Infrastructure** class provides methods to download, configure, and load AI execution providers (EPs) for use with Windows ML or the ONNX Runtime. **Infrastructure** handles the complexity of package management and version selection.

This class is the entry point for your app to access hardware-optimized machine learning acceleration through the Windows ML runtime.

```csharp
namespace Microsoft.Windows.AI.MachineLearning
{
    [contract(Windows.Foundation.UniversalApiContract, 1)]
    [threading(both)]
    [marshaling_behavior(agile)]
    runtimeclass Infrastructure
    {
        // Default constructor, which initializes an instance of the Infrastructure class.
        Infrastructure();

        // Forces the infrastructure to use the version of the EPs that the Windows ML runtime was built with.
        void UseFixedVersions();

        // Downloads package dependencies for the current hardware configuration
        Windows.Foundation.IAsyncAction DownloadPackagesAsync();

        // Loads all EP instances relevant to the current hardware configuration.
        Windows.Foundation.IAsyncOperation<Windows.Foundation.Collections.IIterable<Microsoft.Windows.AI.MachineLearning.ExecutionProvider>> LoadExecutionProvidersAsync();
    }
}
```

### [C# example](#tab/csharp-example)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Download the latest execution provider packages.
await infrastructure.DownloadPackagesAsync();

// Load available execution providers.
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();

// Use execution providers with Windows ML.
var options = new LearningModelSessionOptions();
options.ExecutionProviders(executionProviders);
var session = new LearningModelSession(model, device, options);
```

### [C++/WinRT example](#tab/cppwinrt-example)

```cppwinrt
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Download the latest execution provider packages.
infrastructure.DownloadPackagesAsync().get();

// Load available execution providers.
auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

// Use execution providers with Windows ML.
LearningModelSessionOptions options;
options.ExecutionProviders(executionProviders);
LearningModelSession session{ model, device, options };
```

---

### Infrastructure class constructors

#### Default constructor

**Infrastructure.Infrastructure** is the default constructor, which initializes an instance of the **Infrastructure** class.

### Infrastructure class methods

#### Infrastructure.UseFixedVersions method

Forces the infrastructure to use the version of the execution providers (EPs) that the Windows ML runtime was built with, rather than the latest available versions.

By default, the **Infrastructure** class uses *floating versions* (x.*.*), which allow EPs to be updated with performance improvements and support for new operators. Calling **UseFixedVersions** switches the behavior of the **Infrastructure** class to use *fixed versions* (x.y.*), which provide deterministic behavior by using specific versions of EPs that were tested with the current runtime version.

```csharp
// C# example.
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();
infrastructure.UseFixedVersions();

// Now when calling LoadExecutionProvidersAsync, it will load only the fixed versions.
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();
```

#### Infrastructure.DownloadPackagesAsync method

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers (EPs) for the device's hardware are installed and up-to-date.

* When running in *floating versions* mode (the default), **DownloadPackagesAsync** downloads the latest compatible version of each EP.
* When running in *fixed versions* mode (after calling [**Infrastructure.UseFixedVersions**](#infrastructureusefixedversions-method)), **DownloadPackagesAsync** downloads the specific versions known to be compatible with the current Windows ML runtime.

### [C# example](#tab/csharp-example)

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

### [C++/WinRT example](#tab/cppwinrt-example)

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

#### Infrastructure.LoadExecutionProvidersAsync method

Loads all execution provider (EP) instances relevant to the current hardware configuration. **LoadExecutionProvidersAsync** returns an asynchronous operation that yields the collection of available EPs.

**LoadExecutionProvidersAsync** uses Package Extensions to discover EPs that have declared the `com.microsoft.windowsmlruntime.executionprovider` extension. By default, it loads the latest compatible version of each execution provider (*floating versions*). If **UseFixedVersions** was called, then **LoadExecutionProvidersAsync** will instead load specific versions that were verified with the current Windows ML runtime version.

### [C# example](#tab/csharp-example)

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

### [C++/WinRT ONNX Runtime example](#tab/cppwinrt-example)

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
* Supporting different versions of EPs.

## Package discovery

Execution providers (EPs) are packaged as separate components that declare the `com.microsoft.windowsmlruntime.executionprovider` extension in their package manifests. This design allows EPs to be updated independently from the Windows ML runtime components.

The Windows ML runtime discovers these packages through the Package Extension infrastructure that was introduced in Windows 11. For each discovered EP, the runtime evaluates hardware compatibility, and loads the appropriate implementation for the current system.

## See also

* [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md)
