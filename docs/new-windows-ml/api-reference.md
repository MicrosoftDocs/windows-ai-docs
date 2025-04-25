---
title: Windows ML (Microsoft.Windows.AI.MachineLearning) APIs
description: Windows Machine Learning (ML), in the `Microsoft.Windows.AI.MachineLearning` namespace, provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.topic: article
ms.date: 04/25/2025
---

# Windows ML (Microsoft.Windows.AI.MachineLearning) APIs

Windows Machine Learning (ML), in the `Microsoft.Windows.AI.MachineLearning` namespace, provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps. With Windows ML, your app can download execution providers (EP) that are available from the Microsoft Store, or from anywhere that MSIX packages are provided. You can then load and use those EPs.

This API makes it easier for developers to build applications that use machine learning without having to manage the underlying execution provider packages manually. It handles downloading, updating, and initializing execution providers, which can then be used with frameworks like Microsoft.Windows.AI.MachineLearning and/or the ONNX Runtime.

## Conceptual pages (How To)

### Using Execution Providers with Windows ML Runtime

Windows ML Runtime provides a flexible way to access machine learning execution providers that can optimize ML model inference on different hardware configurations. These execution providers are distributed as separate packages that can be updated independently from the operating system.

### Getting Started with the Microsoft.Windows.AI.MachineLearning NuGet Package

The Microsoft.Windows.AI.MachineLearning API is distributed as a NuGet package, making it easy to integrate into your projects. To get started:

1. Add the Microsoft.Windows.AI.MachineLearning NuGet package to your project
2. Initialize the Infrastructure class
3. Download and load execution providers
4. Use the execution providers with your ML framework

For detailed instructions and the latest release information, refer to the [NuGet Package README](../packaging/WcrNuGet/README.md).

#### NuGet Package Components

The Microsoft.Windows.AI.MachineLearning NuGet package includes:

- C++/WinRT projections for native C++ applications
- WinMD metadata and projections for C# applications
- Required dependencies and redistributable components

#### Project Configuration

For C++ projects:
```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

For C# projects:
```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

See the [NuGet Package README](../packaging/WcrNuGet/README.md) for detailed integration instructions, including platform-specific considerations and minimum system requirements.

#### What are execution providers?

Execution providers are components that implement hardware-specific optimizations for machine learning operations. Execution providers can implement one or more hardware abstractions. For example:
- CPU execution providers optimize for general-purpose processors
- GPU execution providers optimize for graphics processors
- NPU execution providers optimize for neural processing units
- Vendor-specific providers such as OpenVINO, VitisAI, QNN, etc.

The Windows ML Runtime handles the complexity of managing these execution providers by providing APIs to:
1. Download the appropriate execution providers for the current hardware
2. Load execution providers dynamically at runtime
3. Configure execution provider behavior

#### Versioning of Execution Providers

Windows ML Runtime supports two versioning modes for execution providers:

- **Floating versions** (default): Uses the latest compatible version of execution providers matching the same major version (X.*.*). This allows applications to benefit from performance improvements and support for new operators without requiring application changes.

- **Fixed versions**: Uses specific versions of execution providers that were tested with the Windows ML Runtime being used (X.Y.*). This provides deterministic behavior for applications that require consistency.

Execution provider packages follow a Semantic Versioning (SemVer) approach:
- Major and Minor version components are encoded into the Package Name
- Package Version is used for Patch versions

This packaging approach enables the two versioning modes while maintaining compatibility with Store and MSIX deployment mechanisms.

#### ABI Stability

The primary interface between Windows ML Runtime and execution providers is through the ONNX Runtime ABI. Any version of Windows ML Runtime carries a specific version of ONNX Runtime implementing a particular ABI version. Execution provider packages implementing that ABI version and forward (within the same major version) should function properly.

Windows ML Runtime is designed to support at least the past three minor ABI versions at any given time, ensuring backwards compatibility with existing execution provider packages.

#### Basic workflow

The typical workflow for using execution providers involves:

1. Initialize the Microsoft.Windows.AI.MachineLearning.Infrastructure class
2. Call DownloadPackagesAsync() to ensure the latest execution providers are available
3. Call LoadExecutionProvidersAsync() to get the list of execution providers
4. Pass these execution providers to your ML framework (Windows ML or ONNX Runtime)

## API Pages

### Infrastructure class

The Infrastructure class provides methods to download, configure, and load AI execution providers for use with Windows ML or ONNX Runtime. It handles the complexity of package management and version selection.

This class is the entry point for applications to access hardware-optimized machine learning acceleration through the Windows ML Runtime.

```csharp
// C# example
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Download the latest execution provider packages
await infrastructure.DownloadPackagesAsync();

// Load available execution providers
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();

// Use execution providers with Windows ML
var options = new LearningModelSessionOptions();
options.ExecutionProviders(executionProviders);
var session = new LearningModelSession(model, device, options);
```

```cpp
// C++/WinRT example
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Download packages
infrastructure.DownloadPackagesAsync().get();

// Load execution providers
auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

// Use with Windows ML
LearningModelSessionOptions options;
options.ExecutionProviders(executionProviders);
LearningModelSession session{ model, device, options };
```

### Infrastructure.UseFixedVersions method

Forces the infrastructure to use the version of the execution providers that Windows ML Runtime was built with, rather than the latest available versions.

By default, the Infrastructure uses "floating versions" (X.*.*) which allows execution providers to be updated with performance improvements and support for new operators. Calling UseFixedVersions() switches to "fixed versions" (X.Y.*) which provides deterministic behavior by using specific versions of execution providers that were tested with the current runtime version.

```csharp
// C# example
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();
infrastructure.UseFixedVersions();

// Now when calling LoadExecutionProvidersAsync, it will only load the fixed versions
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();
```

### Infrastructure.DownloadPackagesAsync method

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers for the device's hardware are installed and up-to-date.

When running in floating version mode (the default), this method will download the latest compatible version of each execution provider. When running in fixed version mode (after calling UseFixedVersions()), it will download the specific versions known to be compatible with the current Windows ML Runtime.

```csharp
// C# example
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

try {
    // This will download the appropriate packages for the current hardware
    await infrastructure.DownloadPackagesAsync();
    Console.WriteLine("Execution provider packages downloaded successfully");
}
catch (Exception ex) {
    Console.WriteLine($"Failed to download execution provider packages: {ex.Message}");
}
```

```cpp
// C++/WinRT example
try {
    winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;
    
    // Download the packages
    infrastructure.DownloadPackagesAsync().get();
    std::wcout << L"Execution provider packages downloaded successfully\n";
}
catch (const winrt::hresult_error& ex) {
    std::wcout << L"Error: " << ex.message().c_str() << L"\n";
}
```

### Infrastructure.LoadExecutionProvidersAsync method

Loads all execution provider instances relevant to the current hardware configuration. This method returns an asynchronous operation that yields the collection of available execution providers.

This method uses Package Extensions to discover execution providers that have declared the com.microsoft.windowsmlruntime.executionprovider extension. By default, it loads the latest compatible version of each execution provider (floating versions). If UseFixedVersions() was called, it will instead load specific versions that were verified with the current Windows ML Runtime version.

```csharp
// C# example
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// For WinML usage
var executionProviders = await infrastructure.LoadExecutionProvidersAsync();

// Use with Windows ML session options
var sessionOptions = new LearningModelSessionOptions();
sessionOptions.ExecutionProviders(executionProviders);

// Create a session with the configured options
var session = new LearningModelSession(model, device, sessionOptions);
```

```cpp
// C++ ONNX Runtime example
void initialize_windowsml_runtime(OrtSessionOptions* sessionOptions)
{
    // Initialize environment and session
    const auto& api = Ort::GetApi();
    winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;
    
    auto executionProviders = infrastructure.LoadExecutionProvidersAsync().get();

    for (auto ep : executionProviders)
    {
        auto onnxRuntimeExecutionProvider = ep.try_as<IOnnxruntimeExecutionProviderNative>();
        if (onnxRuntimeExecutionProvider)
        {
            // Register the execution provider with ONNX Runtime
            onnxRuntimeExecutionProvider->Register(&api, sessionOptions);
        }
    }
}
```

### Other Infrastructure members

| Name | Description |
|-|-|
| Infrastructure() | Default constructor that initializes an instance of the Infrastructure class |

## API Details

```csharp
namespace Microsoft.Windows.AI.MachineLearning
{
    [contract(Windows.Foundation.UniversalApiContract, 1)]
    [threading(both)]
    [marshaling_behavior(agile)]
    runtimeclass Infrastructure
    {
        // Constructor
        Infrastructure();

        // Force the infrastructure to use the version of the Execution Providers that Windows ML Runtime was built with.
        void UseFixedVersions();

        // Downloads package dependencies for the current hardware.
        Windows.Foundation.IAsyncAction DownloadPackagesAsync();

        // Loads all execution provider instances relevant to the current configuration.
        Windows.Foundation.IAsyncOperation<Windows.Foundation.Collections.IIterable<Microsoft.Windows.AI.MachineLearning.ExecutionProvider>> LoadExecutionProvidersAsync();
    }
}
```

## Appendix

### Implementation Notes

The Infrastructure class handles package management internally using the Windows Store InstallControl APIs which must be called from the main Windows ML Runtime package since it is Microsoft-signed. This includes:

- Resolving available execution providers for the current hardware
- Managing package lifetime and updates
- Handling package registration and activation
- Supporting different versions of execution providers

#### Package Versioning

Execution provider packages follow a Semantic Versioning (SemVer) approach:
- Major and Minor version components are encoded into the Package Name
- Package Version is used for Patch versions

This approach allows Windows ML Runtime to support two versioning modes:
1. **Floating versions (X.*.*)** - The default mode that allows execution providers to be updated with performance improvements and support for the latest operators
2. **Fixed versions (X.Y.*)** - An opt-in mode that ensures deterministic behavior by using specific versions tested with the Windows ML Runtime

#### Package Discovery

Execution providers are packaged as separate components that declare the `com.microsoft.windowsmlruntime.executionprovider` extension in their package manifests. This design allows execution providers to be updated independently from the Windows ML Runtime components.

The Windows ML Runtime discovers these packages through the Package Extension infrastructure introduced in Windows 11. For each discovered execution provider, the runtime evaluates hardware compatibility and loads the appropriate implementation for the current system.
