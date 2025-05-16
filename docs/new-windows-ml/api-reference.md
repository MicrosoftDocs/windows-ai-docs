---
title: Windows ML APIs
description: Learn about the APIs behind Windows Machine Learning (ML) which help your Windows apps run AI models locally.
ms.date: 05/12/2025
ms.topic: article
---

# Windows ML APIs

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

For conceptual guidance, see [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./run-onnx-models.md).

You can think of the APIs in the *Microsoft.Windows.AI.MachineLearning* NuGet package as being the superset of these three sets:

* *New APIs*. Net-new Windows ML APIs, such as the **Infrastructure** class and its methods (which are Windows Runtime APIs); and such as the **WinMLInitialize** function (which is a flat C-style Win32 API, and is one of the Windows ML bootstrap APIs). These APIs are documented in the topic you're reading now.
* *APIs from the older version of Windows ML*. Windows ML APIs that were copied over from the **Windows.AI.MachineLearning** namespace. So for the time being you can learn about those APIs&mdash;with the understanding that they exist now also in **Microsoft.Windows.AI.MachineLearning**&mdash;in the documentation for **Windows.AI.MachineLearning**. See [Windows ML APIs in Windows.AI.MachineLearning](../windows-ml/api-reference.md).
* *ONNX Runtime APIs*. Windows ML implementations (in the *Microsoft.Windows.AI.MachineLearning* NuGet package) of certain APIs from the ONNX Runtime (ORT). For documentation, see the [ONNX Runtime API docs](https://onnxruntime.ai/docs/api/). For example, the [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html). For code examples that use these APIs, and more links to documentation, see the [Use Windows ML to run the ResNet-50 model](./tutorial.md) tutorial.

## The Microsoft.Windows.AI.MachineLearning NuGet package

The Microsoft Windows ML runtime provides APIs for machine learning and AI operations in Windows applications. The *Microsoft.Windows.AI.MachineLearning* NuGet package provides the Windows ML runtime `.winmd` files for use in both C# and C++ projects.

## Net-new Windows Runtime APIs

### Infrastructure class

The **Infrastructure** class provides methods to download, configure, and register AI execution providers (EPs) for use with the ONNX Runtime. **Infrastructure** handles the complexity of package management and hardware selection.

This class is the entry point for your app to access hardware-optimized machine learning acceleration through the Windows ML runtime.

#### [C#](#tab/csharp)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Download the latest execution provider packages
await infrastructure.DownloadPackagesAsync();

// Register available execution providers with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();

// Use ONNX Runtime directly for inference (using Microsoft.ML.OnnxRuntime namespace)
```

#### [C++/WinRT](#tab/cpp)

```cppwinrt
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Download packages
infrastructure.DownloadPackagesAsync().get();

// Register execution providers with ONNX Runtime
infrastructure.RegisterExecutionProviderLibrariesAsync().get();

// Use ONNX Runtime C API directly for inference
```

---

#### Infrastructure class methods

##### Infrastructure.DownloadPackagesAsync method

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers for the device's hardware are installed and up-to-date.

#### [C#](#tab/csharp)

```csharp
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

##### [C++/WinRT](#tab/cpp)

```cppwinrt
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

---

##### Infrastructure.RegisterExecutionProviderLibrariesAsync method

Registers all execution provider libraries relevant to the current hardware configuration with ONNX Runtime. This method should be called before creating ONNX Runtime sessions.

> [!IMPORTANT]
> The Infrastructure instance must stay valid when using ONNX runtime following the call to RegisterExecutionProviderLibrariesAsync.

##### [C#](#tab/csharp)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Register execution providers with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();

// Use ONNX Runtime directly for inference (using Microsoft.ML.OnnxRuntime namespace)
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Register execution providers with ONNX Runtime
infrastructure.RegisterExecutionProviderLibrariesAsync().get();

// Use ONNX Runtime C API directly for inference
```

---

##### Infrastructure.GetExecutionProviderLibraryPathsAsync method

Gets a map of execution provider names to their full file paths. This allows applications to retrieve information about the available execution providers and their locations.

##### [C#](#tab/csharp)

```csharp
// C# example
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

try {
    // Get the map of execution provider names to paths
    var providerPaths = await infrastructure.GetExecutionProviderLibraryPathsAsync();

    foreach (var provider in providerPaths) {
        Console.WriteLine($"Provider: {provider.Key}, Path: {provider.Value}");
    }
}
catch (Exception ex) {
    Console.WriteLine($"Failed to get execution provider paths: {ex.Message}");
}
```

##### [C++/WinRT](#tab/cpp)

```cppwinrt
try {
    winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

    // Get the execution provider paths
    auto providerPaths = infrastructure.GetExecutionProviderLibraryPathsAsync().get();

    for (const auto& pair : providerPaths) {
        std::wcout << L"Provider: " << pair.Key().c_str() << L", Path: " << pair.Value().c_str() << L"\n";
    }
}
catch (const winrt::hresult_error& ex) {
    std::wcout << L"Error: " << ex.message().c_str() << L"\n";
}
```

---

#### Other Infrastructure members

| Name | Description |
|-|-|
| Infrastructure() | Default constructor that initializes an instance of the Infrastructure class |

#### API Details

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

        // Downloads package dependencies for the current hardware.
        Windows.Foundation.IAsyncAction DownloadPackagesAsync();

        // Registers all execution provider libraries with ONNX Runtime.
        Windows.Foundation.IAsyncAction RegisterExecutionProviderLibrariesAsync();
    }
}
```

### Implementation notes

The **Infrastructure** class handles package management internally by using the Microsoft Store **InstallControl** APIs, which must be called from the main Windows ML runtime package, because it's Microsoft-signed. This includes:

* Resolving available execution providers (EPs) for the current hardware configuration.
* Managing package lifetime and updates.
* Handling package registration and activation.
* Supporting different versions of execution providers

#### Package discovery

Execution providers (EPs) are packaged as separate MSIX components that declare the `com.microsoft.windowsmlruntime.executionprovider` extension in their package manifests. This design allows execution providers to be updated independently from the Windows ML Runtime components.

The Windows ML runtime discovers these packages through the Package Extension infrastructure that was introduced in Windows 11. For each discovered EP, the runtime evaluates hardware compatibility, and loads the appropriate implementation for the current system.

#### Using ONNX Runtime with Windows ML Runtime

For C++ applications, after calling `RegisterExecutionProviderLibrariesAsync`, use the ONNX Runtime C API directly to create sessions and run inference.

For C# applications, use the use ONNX Runtime directly for inference using the `Microsoft.ML.OnnxRuntime` namespace.

## Net-new flat C-style Win32 APIs (Windows ML bootstrap APIs)

The following requirements apply to all of the Windows ML bootstrap functions documented below.

|Requirement|Value|
|-|-|
|**NuGet package**|Microsoft.Windows.AI.MachineLearning|
|**Header**|WinMLBootstrap.h|
|**Namespace**|*None*|

### WinMLStatusCallback callback function

```cppwinrt
typedef void (*WinMLStatusCallback)(void* context, HRESULT result);
```

### WinMLInitialize function

```cppwinrt
/**
 * Initializes the WinML runtime, and adds dependencies to the current process.
 * You must call this function before you call any other WinML APIs.
 *
 * @return HRESULT S_OK on success; an error code otherwise.
 */
HRESULT WinMLInitialize(void);
```

### WinMLUninitialize function

```cppwinrt
/**
 * Uninitializes the WinML runtime, and removes any dependencies in the current process.
 * You must call this function before before the process exits.
 *
 * @return No return value.
 */
void WinMLUninitialize(void);
```

### WinMLGetInitializationStatus function

```cppwinrt
/**
 * Returns the initialization status of the WinML runtime.
 * S_OK indicates that the runtime is initialized and ready to use.
 *
 * @return HRESULT S_OK if the runtime is initialized; an error code otherwise.
 */
HRESULT WinMLGetInitializationStatus(void);
```

### WinMLDownloadExecutionProviders function

```cppwinrt
/**
 * Downloads the execution providers applicable to the current device.
 * This function is asynchronous, and will return immediately.
 * A status result will be returned to the callback when the download is complete or has failed.
 *
 * @return HRESULT S_OK on success; an error code otherwise.
 */
HRESULT WinMLDownloadExecutionProviders(
    WinMLStatusCallback onCompletedCallback,
    void* context);
```

### WinMLRegisterExecutionProviders function

```cppwinrt
/**
 * Registers the execution providers applicable to the current device.
 * This function is asynchronous, and will return immediately.
 * A status result will be returned to the callback when the registration is complete or has failed.
 *
 * @return HRESULT S_OK on success, an error code otherwise.
 */
HRESULT WinMLRegisterExecutionProviders(
    WinMLStatusCallback onCompletedCallback,
    void* context);
```

### WinMLDeployMainPackage function

```cppwinrt
/**
 * Deploys the Microsoft.Windows.AI.MachineLearning MSIX package from the
 * msix/win-{arch} directory relative to the application executable.
 *
 * @return HRESULT S_OK on success; an error code otherwise.
 * S_OK is also returned if the package is already installed.
 */
HRESULT WinMLDeployMainPackage();
```

## See also

* [Run ONNX models with Windows ML (Microsoft.Windows.AI.MachineLearning)](./run-onnx-models.md)
