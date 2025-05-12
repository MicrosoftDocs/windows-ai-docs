---
title: Windows ML APIs in Microsoft.Windows.AI.MachineLearning
description: Windows Machine Learning (ML), in the Microsoft.Windows.AI.MachineLearning NuGet package, provides types with which you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.date: 05/09/2025
ms.topic: article
---

# Windows ML APIs in Microsoft.Windows.AI.MachineLearning

For conceptual guidance, see [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md).

You can think of the APIs in the *Microsoft.Windows.AI.MachineLearning* NuGet package as being the superset of these three sets:

* *New APIs*. Net-new Windows ML APIs, such as the **Infrastructure** class and its methods (which are Windows Runtime APIs); and such as the **WinMLInitialize** function (which is a flat C-style Win32 API, and is one of the Windows ML bootstrap APIs). These APIs are documented in the topic you're reading now.
* *APIs from the older version of Windows ML*. Windows ML APIs that were copied over from the **Windows.AI.MachineLearning** namespace. So for the time being you can learn about those APIs&mdash;with the understanding that they exist now also in **Microsoft.Windows.AI.MachineLearning**&mdash;in the documentation for **Windows.AI.MachineLearning**. See [Windows ML APIs in Windows.AI.MachineLearning](../windows-ml/api-reference.md).
* *ONNX Runtime APIs*. Windows ML implementations (in the *Microsoft.Windows.AI.MachineLearning* NuGet package) of certain APIs from the ONNX Runtime (ORT). For documentation, see the [ONNX Runtime API docs](https://onnxruntime.ai/docs/api/). For example, the [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html). For code examples that use these APIs, and more links to documentation, see the [Use Windows ML to run the ResNet-50 model](./tutorial.md) tutorial.

## The Microsoft.Windows.AI.MachineLearning NuGet package

The Microsoft Windows ML runtime provides APIs for machine learning and AI operations in Windows applications. The *Microsoft.Windows.AI.MachineLearning* NuGet package provides the Windows ML runtime `.winmd` files for use in both C# and C++ projects.

### Deploying the package

The package includes MSIX packages that contain the Windows ML runtime. You should deploy these packages as part of your application's installation process.

#### MSIX package structure

When `WinMLDeployMSIXToOutput` is set to `true`, the MSIX packages will be copied to your output directory in this structure:

```
msix/
├── win-x64/
│   └── Microsoft.Windows.AI.MachineLearning.msix
└── win-arm64/
    └── Microsoft.Windows.AI.MachineLearning.msix
```

You can instruct the NuGet package to copy these files to your output directory by setting:

```xml
<PropertyGroup>
  <!-- Copy architecture-specific MSIX files to output directory -->
  <WinMLDeployMSIXToOutput>true</WinMLDeployMSIXToOutput>
</PropertyGroup>
```

#### Hardware architecture detection

> [!IMPORTANT]
> You must install the MSIX package that matches your hardware platform, not your application's architecture. For ARM64 hardware, use the ARM64 package, even if your application is x64 (running under emulation). For x64 hardware, use the x64 package.

### [C# deployment code example](#tab/csharp-1)

```csharp
using Microsoft.Windows.AI.MachineLearning.Bootstrap;

// Deploy the package.
// Auto-detects hardware architecture, and uses the appropriate MSIX.
int hr = NativeMethods.WinMLDeployMainPackage();
if (hr < 0)
{
    // Handle deployment failure.
}
```

### [C++ deployment code example](#tab/cpp-1)

```cpp
#include <WinMLBootstrap.h>

// Deploy the package.
// Auto-detects hardware architecture, and uses the appropriate MSIX.
HRESULT hr = WinMLDeployMainPackage();
if (FAILED(hr))
{
    // Handle deployment failure.
}
```

---

> [!IMPORTANT]
> The MSIX deployment function is specifically designed for installation scenarios. It automatically looks for the MSIX file in the `msix/win-{arch}` subdirectory relative to your application executable.

#### Custom deployment

If you need more control over the deployment process, then you can use the [**Windows.Management.Deployment.PackageManager**](/uwp/api/windows.management.deployment.packagemanager) class directly.

### Bootstrap functionality

The package includes bootstrap functionality for both C# and C++ projects, which is automatically configured when you install the NuGet package. This provides runtime initialization and dependency management without any additional setup.

#### What's included

For C# projects, the package automatically:

* Copies the `WinMLBootstrap.dll` to your output directory
* Includes auto-initialization code.

For C++ projects, the package automatically:

* Adds necessary include paths.
* Adds required library references (`WinMLBootstrap.lib`).
* Copies the `WinMLBootstrap.dll` to your output directory
* Includes auto-initialization code.

#### Configuration options

Bootstrap functionality is included by default, but you can configure its behavior with these project properties:

```xml
<PropertyGroup>
  <!-- Disable auto-initialization completely -->
  <WinMLBootstrapAutoInitializeDisabled>true</WinMLBootstrapAutoInitializeDisabled>

  <!-- Allow execution to continue even if initialization fails (default: false) -->
  <WinMLContinueOnInitFailure>true</WinMLContinueOnInitFailure>
</PropertyGroup>
```

##### 1. Disable auto-initialization

When `WinMLBootstrapAutoInitializeDisabled` is set to `true`, the auto-initialization code is not included in your project. In this case, you'll need to manually initialize and uninitialize the runtime as shown below.

##### 2. Continue on initialization failure

When `WinMLContinueOnInitFailure` is set to `true`, your application will continue running even if initialization fails. You can check the initialization status using the methods shown in the manual initialization examples.

##### Manual initialization

When auto-initialization is disabled, you need to manually initialize and uninitialize.

### [C# manual initialization](#tab/csharp-2)

```csharp
using Microsoft.Windows.AI.MachineLearning.Bootstrap;

// Initialize
int hr = NativeMethods.WinMLInitialize();
if (hr < 0)
{
    // Handle initialization failure...
}

// Execution provider download and registration should use the WinRT API.

// Get the initialization status at any point.
int status = NativeMethods.WinMLGetInitializationStatus();

// Later, uninitialize before application exit.
NativeMethods.WinMLUninitialize();
```

### [C++ manual initialization](#tab/cpp-2)

```cpp
#include <WinMLBootstrap.h>

// Initialize
HRESULT hr = WinMLInitialize();
if (FAILED(hr))
{
    // Handle initialization failure...
}

// Get the initialization status at any point.
HRESULT status = WinMLGetInitializationStatus();

// On first application run attempt to download necessary ExecutionProviderPackages.
HANDLE downloadEvent = CreateEvent(nullptr, FALSE, FALSE, nullptr);
auto downloadCallback = [](void* context, HRESULT result)
{
    HANDLE event = static_cast<HANDLE>(context);
    if (FAILED(result))
    {
        // Handle failure to download execution provider packages.
        exit(-1);
    }
    SetEvent(event);
};

status = WinMLDownloadExecutionProviders(downloadCallback, &downloadContext);
if (SUCCEEDED(status))
{
    if (!WaitForSingleObject(downloadEvent, 120 * 1000))
    {
        if (GetLastError() == WAIT_TIMEOUT)
        {
            // Handle download timeouts here.
            return;
        }
    }
}
else
{
    // Handle failures to download execution provider packages.
}

// Before interacting with Ort register the execution providers.
HANDLE dEvent = CreateEvent(nullptr, FALSE, FALSE, nullptr);
auto registerCallback = [](void* context, HRESULT result)
{
    HANDLE event = static_cast<HANDLE>(context);
    if (FAILED(result))
    {
        // Handle failure to register execution providers.
        exit(-1);
    }
    SetEvent(event);
};

status = WinMLRegisterExecutionProviders(registerCallback, &downloadContext);
if (SUCCEEDED(status))
{
    WaitForSingleObject(registerEvent, INFINITE);
}
else
{
    // Handle failure to register execution providers.
}

// Now interact with Ort APIs.

...

// Later, uninitialize before application exit.
WinMLUninitialize();
```

---

## Net-new Windows Runtime APIs

### Infrastructure class

The **Infrastructure** class provides methods to download, configure, and register AI execution providers (EPs) for use with the ONNX Runtime. **Infrastructure** handles the complexity of package management and hardware selection.

This class is the entry point for your app to access hardware-optimized machine learning acceleration through the Windows ML runtime.

#### [C# example](#tab/csharp-example-1)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Download the latest execution provider packages
await infrastructure.DownloadPackagesAsync();

// Register available execution providers with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();

// Use ONNX Runtime directly for inference (using Microsoft.ML.OnnxRuntime namespace)
```

#### [C++/WinRT example](#tab/cppwinrt-example-1)

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

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers (EPs) for the device's hardware are installed and up-to-date.

* **DownloadPackagesAsync** downloads the latest compatible version of each EP.

##### [C# example](#tab/csharp-example-2)

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

##### [C++/WinRT example](#tab/cppwinrt-example-2)

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

##### Infrastructure.DownloadPackagesAsync method

Downloads package dependencies for the current hardware configuration. This ensures that the appropriate execution providers for the device's hardware are installed and up-to-date.

#### [C# example](#tab/csharp-example-3)

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

##### [C++/WinRT example](#tab/cppwinrt-example-3)

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

##### [C# example](#tab/csharp-example-4)

```csharp
var infrastructure = new Microsoft.Windows.AI.MachineLearning.Infrastructure();

// Register execution providers with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();

// Use ONNX Runtime directly for inference (using Microsoft.ML.OnnxRuntime namespace)
```

##### [C++/WinRT example](#tab/cppwinrt-example-4)

```cppwinrt
winrt::Microsoft::Windows::AI::MachineLearning::Infrastructure infrastructure;

// Register execution providers with ONNX Runtime
infrastructure.RegisterExecutionProviderLibrariesAsync().get();

// Use ONNX Runtime C API directly for inference
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

```cpp
typedef void (*WinMLStatusCallback)(void* context, HRESULT result);
```

### WinMLInitialize function

```cpp
/**
 * Initializes the WinML runtime, and adds dependencies to the current process.
 * You must call this function before you call any other WinML APIs.
 *
 * @return HRESULT S_OK on success; an error code otherwise.
 */
HRESULT WinMLInitialize(void);
```

### WinMLUninitialize function

```cpp
/**
 * Uninitializes the WinML runtime, and removes any dependencies in the current process.
 * You must call this function before before the process exits.
 *
 * @return No return value.
 */
void WinMLUninitialize(void);
```

### WinMLGetInitializationStatus function

```cpp
/**
 * Returns the initialization status of the WinML runtime.
 * S_OK indicates that the runtime is initialized and ready to use.
 *
 * @return HRESULT S_OK if the runtime is initialized; an error code otherwise.
 */
HRESULT WinMLGetInitializationStatus(void);
```

### WinMLDownloadExecutionProviders function

```cpp
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

```cpp
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

```cpp
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

* [Get started with Windows ML (Microsoft.Windows.AI.MachineLearning)](./get-started.md)
