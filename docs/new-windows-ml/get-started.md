---
title: Get started with Windows ML
description: With Windows Machine Learning (ML) types in the Microsoft.Windows.AI.MachineLearning namespace, you can build hardware-abstracted AI inferencing capabilities into your Windows apps.
ms.date: 05/13/2025
ms.topic: article
---

# Get started with Windows ML

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

For API reference content, see [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md).

With Windows Machine Learning (ML) types in the **Microsoft.Windows.AI.MachineLearning** namespace, you can build hardware-abstracted AI inferencing capabilities into your Windows apps. With Windows ML, your app can download execution providers (EPs) that are available from the Microsoft Store, or from any MSIX source. You can then register and use those EPs.

The APIs in **Microsoft.Windows.AI.MachineLearning** make it easier for you as a developer to build apps that use machine learning (ML) without your having to manually manage the underlying execution provider (EP) packages. The APIs handle downloading, updating, and initializing EPs; which you can then continue to use with **Microsoft.Windows.AI.MachineLearning**, and/or with the [ONNX Runtime](https://onnxruntime.ai/).

## Using execution providers with Windows ML

The Windows ML runtime provides a flexible way to access machine learning (ML) execution providers (EPs), which can optimize ML model inference on different hardware configurations. Those EPs are distributed as separate packages that can be updated independently from the operating system.

## The Microsoft.Windows.AI.MachineLearning NuGet package

The APIs in **Microsoft.Windows.AI.MachineLearning** are distributed as a NuGet package, making it easy to integrate into your projects. This topic covers in detail the following high-level steps:

1. In Visual Studio, add the *Microsoft.Windows.AI.MachineLearning* NuGet package to your project. For the latest release info, see the `README.md` file included with the NuGet package.
2. Initialize the **Infrastructure** class.
3. Download and register execution providers (EPs).
4. Use the EPs with your ML framework.

## NuGet package components

The *Microsoft.Windows.AI.MachineLearning* NuGet package includes the following:

* C++/WinRT projections for native C++ apps.
* WinMD metadata and projections for C# apps.
* Required dependencies and redistributable components.

## Project configuration

For detailed integration instructions, including platform-specific considerations and minimum system requirements, see the `README.md` file included with the NuGet package.

### [C# Visual Studio project](#tab/csharp-projects)

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

### [C++/WinRT Visual Studio project](#tab/cppwinrt-projects)

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

---

## What is an execution provider?

An execution provider (EP) is a component that implements hardware-specific optimizations for machine learning (ML) operations. An EP can implement one or more hardware abstractions. For example:

* CPU execution providers optimize for general-purpose processors.
* GPU execution providers optimize for graphics processors.
* NPU execution providers optimize for neural processing units.
* Vendor-specific providers such as OpenVINO, VitisAI, QNN, and others.

The Windows ML runtime handles the complexity of managing those execution providers by providing APIs to do the following:

1. Download the appropriate EPs for the current hardware.
2. Register EPs dynamically at runtime.
3. Configure EP behavior.

## Selecting execution providers for inference

As of the 1.22 release, the ONNX Runtime has introduced new APIs to enable more control over selecting EPs managed by the Windows ML runtime. The APIs allow apps to configure EPs automatically based on a simple selection policy or explicitly, allowing for more control over provider options and which devices should be used.

For more details see the [ONNX Runtime OrtApi documentation](https://onnxruntime.ai/docs/api/c/struct_ort_api.html). 

### Automatic EP selection

Let the ONNX Runtime select the best device for inference using a simple policy via the `SessionOptionsSetEpSelectionPolicy` function on the `OrtApi` using the `OrtExecutionProviderDevicePolicy` values.

#### [C# code example](#tab/csharp-1)

```csharp
using Microsoft.ML.OnnxRuntime;

// Configure the session to select an EP and device for MAX_EFFICIENCY which typically
// will choose an NPU if available with a CPU fallback.
var sessionOptions = new SessionOptions();
sessionOptions.SetEpSelectionPolicy(ExecutionProviderDevicePolicy.MAX_EFFICIENCY);

```

#### [C++ code example](#tab/cpp-1)

```cpp
#include <win_onnxruntime_cxx_api.h>

// Configure the session to select an EP and device for MAX_PERFORMANCE which typically
// will choose an GPU if available with a CPU fallback.
Ort::SessionOptions sessionOptions;
sessionOptions.SetEpSelectionPolicy(OrtExecutionProviderDevicePolicy_MAX_PERFORMANCE);
```

---

### Explicit EP selection and provider options

If your app requires explicit selection of one or more EPs, including the need to set provider options on an EP, the ONNX Runtime APIs allow for this using the `GetEpDevices` function on `OrtApi` which enables enumerating through all available devices. `SessionOptionsAppendExecutionProvider_V2` can then be used to explicitly append specific devices and provide custom provider options to the desired EP.

#### [C# code example](#tab/csharp-1)

```csharp
using Microsoft.ML.OnnxRuntime;

// Get all available EP devices from the environment
using var ortEnv = OrtEnv.Instance();
var epDevices = environment.GetEpDevices();

// Accumulate devices by EpName
// Passing all devices for a given EP in a single call allows the execution provider
// to select the best configuration or combination of devices, rather than being limited
// to a single device. This enables optimal use of available hardware if supported by the EP.
var epDeviceMap = epDevices
    .GroupBy(device => device.EpName)
    .ToDictionary(g => g.Key, g => g.ToList());

// For demonstration, list all found EPs, vendors, and device types
foreach (var epGroup in epDeviceMap)
{
    var epName = epGroup.Key;
    var devices = epGroup.Value;

    Console.WriteLine($"Execution Provider: {epName}");
    foreach (var device in devices)
    {
        string deviceType = GetDeviceTypeString(device.HardwareDevice.Type);
        Console.WriteLine($" | Vendor: {device.EpVendor,-16} | Device Type: {deviceType,-8}");
    }
}

// Configure and append each EP type only once, with all its devices
var sessionOptions = new SessionOptions();
foreach ((var epName, var devices) in epDeviceMap)
{
    Dictionary<string, string> epOptions = new();
    switch (epName)
    {
        case "VitisAIExecutionProvider":
            // Demonstrating passing no options for VitisAI
            sessionOptions.AppendExecutionProvider(environment, devices, epOptions);
            Console.WriteLine($"Successfully added {epName} EP");
            break;

        case "OpenVINOExecutionProvider":
            // Configure threading for OpenVINO EP, pick the first device found
            epOptions["num_of_threads"] = "4";
            sessionOptions.AppendExecutionProvider(environment, [devices.First()], epOptions);
            Console.WriteLine($"Successfully added {epName} EP (first device only)");
            break;

        case "QNNExecutionProvider":
            // Configure performance mode for QNN EP
            epOptions["htp_performance_mode"] = "high_performance";
            sessionOptions.AppendExecutionProvider(environment, devices, epOptions);
            Console.WriteLine($"Successfully added {epName} EP");
            break;

        default:
            Console.WriteLine($"Skipping EP: {epName}");
            break;
    }
}

```

#### [C++ code example](#tab/cpp-1)

```cpp
#include <win_onnxruntime_cxx_api.h>

// Get all available EP devices from the environment
Ort::Env env(ORT_LOGGING_LEVEL_INFO, "CppEnvironment");
std::vector<Ort::ConstEpDevice> ep_devices = env.GetEpDevices();

// Accumulate devices by ep_name
// Passing all devices for a given EP in a single call allows the execution provider
// to select the best configuration or combination of devices, rather than being limited
// to a single device. This enables optimal use of available hardware if supported by the EP.
std::unordered_map<std::string, std::vector<Ort::ConstEpDevice>> ep_device_map;
for (const auto& device : ep_devices)
{
    ep_device_map[device.EpName()].push_back(device);
}

// For demonstration, list all found EPs, vendors, and device types
for (const auto& [ep_name, devices] : ep_device_map)
{
    std::cout << "Execution Provider: " << ep_name << std::endl;
    for (const auto& device : devices)
    {
        std::cout << " | Vendor: " << std::setw(16) << device.EpVendor() << " | Device Type: " << std::setw(8)
                    << ToString(device.Device().Type()) << std::endl;
    }
}

// Configure and append each EP type only once, with all its devices
Ort::SessionOptions session_options;
for (const auto& [ep_name, devices] : ep_device_map)
{
    Ort::KeyValuePairs ep_options;
    if (ep_name == "VitisAIExecutionProvider")
    {
        // Demonstrating passing no options for VitisAI
        session_options.AppendExecutionProvider_V2(env, devices, ep_options);
    }
    else if (ep_name == "OpenVINOExecutionProvider")
    {
        // Configure threading for OpenVINO EP, pick the first device found.
        ep_options.Add("num_of_threads", "4");
        session_options.AppendExecutionProvider_V2(env, {devices.front()}, ep_options);
    }
    else if (ep_name == "QNNExecutionProvider")
    {
        // Configure performance mode for QNN EP
        ep_options.Add("htp_performance_mode", "high_performance");
        session_options.AppendExecutionProvider_V2(env, devices, ep_options);
    }
    else
    {
        std::cout << "Skipping EP: " << ep_name << std::endl;
    }
}
```

---

## Versioning of execution providers

The Windows ML runtime uses the latest compatible version of EPs matching the same major version (x.*.*). This allows apps to benefit from performance improvements and support for new operators without requiring changes to your app.

EP packages follow a semantic versioning (SemVer) approach:
* Major and minor version components are encoded into the *Package Name*.
* *Package Version* is used for patch versions.

That packaging approach enables flexible versioning while maintaining compatibility with Microsoft Store and MSIX deployment mechanisms.

## ABI stability

The primary interface between the Windows ML runtime and execution providers (EPs) is through the ONNX Runtime ABI. Any version of the Windows ML runtime carries a specific version of the ONNX Runtime implementing a particular ABI version. EP packages implementing that ABI version and later (within the same major version) will function properly.

The Windows ML runtime is designed to support at least the past three minor ABI versions at any given time; ensuring backwards compatibility with existing EP packages.

## Model compilation

An ONNX model is represented as a graph, where nodes correspond to operators (such as matrix multiplications, convolutions, and other mathematical processes), and edges define the flow of data between them.

This graph-based structure allows for efficient execution and optimization by allowing transformations such as operator fusion (that is, combining multiple related operations into a single optimized operation) and graph pruning (that is, removing redundant nodes from the graph).

Model compilation refers to the process of transforming an ONNX model with the aid of an execution provider (EP) into an optimized representation that can be executed efficiently on the device's underlying hardware.

### API support for compilation

As of the 1.22 release, the ONNX Runtime has introduced new APIs to better encapsulate the compilation steps. More details are available in the ONNX Runtime compile documentation (see [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html)).

At a high level, once you've gone through EP selection, your application code should perform the following steps:

1. **Retrieve a pointer to the Compile API**. This is done via the **GetCompileApi** method of the **OrtApi** type.
1. **Create compilation options from session options**. This is done via the **CreateModelCompilationOptionsFromSessionOptions** function of the **OrtCompileApi** type. On the compile options, you can set the input `.onnx` model file path, and specify where the compiled version should be saved.
1. **Compile the model**. Generate the compiled model by invoking the **CompileModel** API with the configured options.
1. **Create a new session using the compiled model**. The compiled model can then be loaded into an ONNX Runtime session for inference.

For the steps and associated code for this process, see [Tutorial and code example](./tutorial.md), and see the code snippets below.

#### [C# code example](#tab/csharp-2)

```csharp
using Microsoft.ML.OnnxRuntime;

// Set up environment (elided for brevity)

// Set up session options and EP selection policy, for example:
SessionOptions sessionOptions = new();
sessionOptions.SetEpSelectionPolicy(ExecutionProviderDevicePolicy.PREFER_NPU);

// Prepare compilation options
OrtModelCompilationOptions compileOptions = new(sessionOptions);
compileOptions.SetInputModelPath(modelPath);
compileOptions.SetOutputModelPath(compiledModelPath);

// Compile the model
compileOptions.CompileModel();

// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

#### [C++ code example](#tab/cpp-2)

```cpp
#include <win_onnxruntime_cxx_api.h>

// Set up environment (elided for brevity)

// Set up session options and EP selection policy, e.g.:
Ort::SessionOptions sessionOptions;
sessionOptions.SetEpSelectionPolicy(OrtExecutionProviderDevicePolicy_PREFER_NPU);

const OrtCompileApi* compileApi = ortApi.GetCompileApi();

// Prepare compilation options
OrtModelCompilationOptions* compileOptions = nullptr;
OrtStatus* status = compileApi->CreateModelCompilationOptionsFromSessionOption(env, sessionOptions, &compileOptions);
status = compileApi->ModelCompilationOptions_SetInputModelPath(compileOptions, modelPath.c_str());
status = compileApi->ModelCompilationOptions_SetOutputModelPath(compileOptions, compiledModelPath.c_str());

// Compile the model
status = compileApi->CompileModel(env, compileOptions);

// Clean up
compileApi->ReleaseModelCompilationOptions(compileOptions);

// Create inference session using compiled model
Ort::Session session(env, compiledModelPath.c_str(), sessionOptions);
```

---

### Designing for compilation

Here are some ideas for handling compilation in your application.

* **Compilation performance**. Compilation can take several minutes to complete. So that any UI remains responsive, consider doing this as a background operation in your application.
* **User interface updates**. Consider letting your users know whether your application is doing any compilation work, and notifying them when it's complete.  
* **Graceful fallback mechanisms**. If there is an issue with loading a compiled model, then try to capture diagnostic data for the failure, and have your application fall back to using the original model if possible so that your application's related AI functionality can still be used.  

## Basic workflow

The typical workflow for using execution providers (EPs) involves these steps:

1. Initialize the **Microsoft.Windows.AI.MachineLearning.Infrastructure** class.
2. Call **DownloadPackagesAsync** to ensure that the latest execution providers are available.
3. Call **RegisterExecutionProviderLibrariesAsync** to register EPs with the ONNX Runtime.
4. Use ONNX Runtime APIs directly for model inference.

## See also

* [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md)
