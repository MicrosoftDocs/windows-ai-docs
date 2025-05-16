---
title: Distributing your app that uses Windows ML
description: Learn how to distribute your app that uses Windows Machine Learning (ML).
ms.date: 05/13/2025
ms.topic: article
---

# Distributing your app that uses Windows ML

When you're ready to distribute your app that uses Windows ML, you will have to follow a few additional steps to ensure that the Windows ML Runtime is deployed to your users' devices. When deploying your app on your own device, the NuGet package automatically sets this up on your own device.

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

## MSIX package structure

The NuGet package includes MSIX packages that contain the Windows ML runtime. You should deploy these packages as part of your application's installation process.

You can instruct the NuGet package to copy these files to your output directory by setting the `WinMLDeployMSIXToOutput` to `true`:

```xml
<PropertyGroup>
  <!-- Copy architecture-specific MSIX files to output directory -->
  <WinMLDeployMSIXToOutput>true</WinMLDeployMSIXToOutput>
</PropertyGroup>
```

The MSIX packages will then be copied to your output directory in this structure...

```
msix/
├── win-x64/
│   └── Microsoft.Windows.AI.MachineLearning.msix
└── win-arm64/
    └── Microsoft.Windows.AI.MachineLearning.msix
```

## Hardware architecture detection

> [!IMPORTANT]
> You must install the MSIX package that matches your hardware platform, not your application's architecture. For ARM64 hardware, use the ARM64 package, even if your application is x64 (running under emulation). For x64 hardware, use the x64 package.

### [C#](#tab/csharp)

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

### [C++/WinRT](#tab/cpp)

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

## Custom deployment

If you need more control over the deployment process, then you can use the [**Windows.Management.Deployment.PackageManager**](/uwp/api/windows.management.deployment.packagemanager) class directly.

## Bootstrap functionality

The package includes bootstrap functionality for both C# and C++ projects, which is automatically configured when you install the NuGet package. This provides runtime initialization and dependency management without any additional setup.

### [C#](#tab/csharp)

For C# projects, the package automatically:

* Copies the `WinMLBootstrap.dll` to your output directory
* Includes auto-initialization code.

### [C++/WinRT](#tab/cpp)

For C++ projects, the package automatically:

* Adds necessary include paths.
* Adds required library references (`WinMLBootstrap.lib`).
* Copies the `WinMLBootstrap.dll` to your output directory
* Includes auto-initialization code.

---

### Configuration options

Bootstrap functionality is included by default, but you can configure its behavior with these project properties:

```xml
<PropertyGroup>
  <!-- Disable auto-initialization completely -->
  <WinMLBootstrapAutoInitializeDisabled>true</WinMLBootstrapAutoInitializeDisabled>

  <!-- Allow execution to continue even if initialization fails (default: false) -->
  <WinMLContinueOnInitFailure>true</WinMLContinueOnInitFailure>
</PropertyGroup>
```

#### 1. Disable auto-initialization

When `WinMLBootstrapAutoInitializeDisabled` is set to `true`, the auto-initialization code is not included in your project. In this case, you'll need to manually initialize and uninitialize the runtime as shown below.

#### 2. Continue on initialization failure

When `WinMLContinueOnInitFailure` is set to `true`, your application will continue running even if initialization fails. You can check the initialization status using the methods shown in the manual initialization examples.

#### Manual initialization

When auto-initialization is disabled, you need to manually initialize and uninitialize.

##### [C#](#tab/csharp)

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

##### [C++/WinRT](#tab/cpp)

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