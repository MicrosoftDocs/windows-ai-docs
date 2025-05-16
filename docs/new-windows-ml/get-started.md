---
title: Get started with Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local AI models in your Windows apps.
ms.date: 05/13/2025
ms.topic: article
---

# Get started with Windows ML

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

With the Windows Machine Learning (ML) types in the **Microsoft.Windows.AI.MachineLearning** namespace, you can build hardware-abstracted AI inferencing capabilities into your Windows apps without your having to manually manage the underlying execution provider (EP) packages. The APIs handle downloading, updating, and initializing EPs; which you can then continue to use with **Microsoft.Windows.AI.MachineLearning**, and/or with the [ONNX Runtime](https://onnxruntime.ai/).

## Prerequisites

* Windows 11 PC running version 24H2 (build 26100) or greater.

In addition to the above, there are language-specific prerequisites depending on what language your app is written in.

### [C#](#tab/csharp)

* .NET 8 or greater
* Targeting TFM `windows10.0.26100` or greater

### [C++/WinRT](#tab/cppwinrt)

C++20 or later.

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Install the WinML package

### [C#](#tab/csharp)

In your Visual Studio project, add the *Microsoft.Windows.AI.MachineLearning* NuGet package to your project.

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

And then import the namespaces in your code.

```csharp
using Microsoft.ML.OnnxRuntime;
using Microsoft.Windows.AI.MachineLearning;
```

### [C++/WinRT](#tab/cppwinrt)

In your Visual Studio project, add the *Microsoft.Windows.AI.MachineLearning* NuGet package to your project.

```xml
<PackageReference Include="Microsoft.Windows.AI.MachineLearning" Version="x.y.z" />
```

And then add the OnnxRuntime header file to your code.

```cppwinrt
#include <win_onnxruntime_cxx_api.h>
```


### [Python](#tab/python)

Windows ML provides a Python binding called `onnxruntime-winml`, which has Python support for EP acquisition and configuration. Once set up, Python applications can use ONNX runtime features like auto EP selection as usual.

```powershell
pip install --index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple --extra-index-url https://pypi.org/simple onnxruntime-winml
```

And then import the OnnxRuntime module in your code.

```python
import onnxruntime as ort
```

---

## Step 2: Download and register the latest EPs

Then, we will use Windows ML to ensure that the latest execution providers (EPs) are available on the device and registered with the ONNX Runtime.

### [C#](#tab/csharp)

```csharp
// Initialize Windows ML infrastructure
Infrastructure infrastructure = new();

// Ensure the latest execution providers are available (downloads them if they aren't)
await infrastructure.DownloadPackagesAsync();

// And register the EPs with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();
```

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
// Initialize Windows ML infrastructure
winrt::Microsoft.Windows.AI.MachineLearning::Infrastructure infrastructure{};

// Ensure the latest execution providers are available (downloads them if they aren't)
co_await infrastructure.DownloadPackagesAsync();

// And register the EPs with ONNX Runtime
co_await infrastructure.RegisterExecutionProviderLibrariesAsync();
```

### [Python](#tab/python)

For Python, the EPs are automatically downloaded and registered as part of the first import. You don't need to do anything additional.

---

## Step 3: Configure automatic EP selection

As of the 1.22 release, the ONNX Runtime allow apps to configure execution providers (EPs) automatically based on a simple selection policy or explicitly, allowing for more control over provider options and which devices should be used.

For now, we will let the ONNX Runtime select the best device for inference using a simple policy via the `SessionOptionsSetEpSelectionPolicy` function on the `OrtApi` using the `OrtExecutionProviderDevicePolicy` values. There are a variety of values you can use for automatic selection, like `MAX_PERFORMANCE`, `PREFER_NPU`, `MAX_EFFICIENCY`, and more. See the [ONNX OrtExecutionProviderDevicePolicy docs](https://onnxruntime.ai/docs/api/c/group___global.html#gaf26ca954c79d297a31a66187dd1b4e24) for other values you can use. Alternatively, you can learn more about explicit selection in the [explicit EP selection](#explicit-ep-selection-and-provider-options) section below.

### [C#](#tab/csharp)

```csharp
// Configure the session to select an EP and device for MAX_EFFICIENCY which typically
// will choose an NPU if available with a CPU fallback.
var sessionOptions = new SessionOptions();
sessionOptions.SetEpSelectionPolicy(ExecutionProviderDevicePolicy.MAX_EFFICIENCY);
```

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
// Configure the session to select an EP and device for MAX_EFFICIENCY which typically
// will choose an NPU if available with a CPU fallback.
Ort::SessionOptions sessionOptions;
sessionOptions.SetEpSelectionPolicy(OrtExecutionProviderDevicePolicy_MAX_EFFICIENCY);
```

### [Python](#tab/python)

```python
# Configure the session to select an EP and device for MAX_EFFICIENCY which typically
# will choose an NPU if available with a CPU fallback.
options = ort.SessionOptions()
options.set_provider_selection_policy(ort.OrtExecutionProviderDevicePolicy.MAX_EFFICIENCY)
assert options.has_providers()
```

---

## Step 4: Compile the model

ONNX models must be compiled into an optimized representation that can be executed efficiently on the device's underlying hardware. The excecution provider you configured in step 3 helps perform this transformation.

As of the 1.22 release, the ONNX Runtime has introduced new APIs to better encapsulate the compilation steps. More details are available in the ONNX Runtime compile documentation (see [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html)).

> [!NOTE]
> Compilation can take several minutes to complete. So that any UI remains responsive, consider doing this as a background operation in your application.

#### [C#](#tab/csharp)

```csharp
// Prepare compilation options using our session we configured in step 3
OrtModelCompilationOptions compileOptions = new(sessionOptions);
compileOptions.SetInputModelPath(modelPath);
compileOptions.SetOutputModelPath(compiledModelPath);

// Compile the model
compileOptions.CompileModel();
```

#### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
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
```

#### [Python](#tab/python)

```python
input_model_path = "path_to_your_model.onnx"
output_model_path = "path_to_your_compiled_model.onnx"

model_compiler = ort.ModelCompiler(
    options,
    input_model_path,
    embed_compiled_data_into_model=True,
    external_initializers_file_path=None,
)
model_compiler.compile_to_file(output_model_path)
assert os.path.exists(output_model_path)
```

---

## Step 5: Inference the model

Now that the model is compiled for the local hardware on the device, we can create an inference session and inference the model.

#### [C#](#tab/csharp)

```csharp
// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

#### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
// Create inference session using compiled model
Ort::Session session(env, compiledModelPath.c_str(), sessionOptions);
```

#### [Python](#tab/python)

```python
# Create inference session using compiled model
session = ort.InferenceSession(output_model_path, sess_options=options)
```

---

## Step 6: Distributing your app

Before distributing your app, C# and C++ developers will need to take additional steps to ensure the Windows ML Runtime is installed on your users' devices when your app is installed. See the [distributing your app](./distributing-your-app.md) page to learn more.


## Model compilation

An ONNX model is represented as a graph, where nodes correspond to operators (such as matrix multiplications, convolutions, and other mathematical processes), and edges define the flow of data between them.

This graph-based structure allows for efficient execution and optimization by allowing transformations such as operator fusion (that is, combining multiple related operations into a single optimized operation) and graph pruning (that is, removing redundant nodes from the graph).

Model compilation refers to the process of transforming an ONNX model with the aid of an execution provider (EP) into an optimized representation that can be executed efficiently on the device's underlying hardware.

### Designing for compilation

Here are some ideas for handling compilation in your application.

* **Compilation performance**. Compilation can take several minutes to complete. So that any UI remains responsive, consider doing this as a background operation in your application.
* **User interface updates**. Consider letting your users know whether your application is doing any compilation work, and notifying them when it's complete.  
* **Graceful fallback mechanisms**. If there is an issue with loading a compiled model, then try to capture diagnostic data for the failure, and have your application fall back to using the original model if possible so that your application's related AI functionality can still be used.  

## Explicit EP selection and provider options

The ONNX Runtime allow apps to configure execution providers (EPs) automatically based on a simple selection policy or explicitly, allowing for more control over provider options and which devices should be used.

For more details see the [ONNX Runtime OrtApi documentation](https://onnxruntime.ai/docs/api/c/struct_ort_api.html). To learn about the versioning strategy around EPs, see the [versioning of execution providers documentation](./versioning.md).

If your app requires explicit selection of one or more EPs, including the need to set provider options on an EP, the ONNX Runtime APIs allow for this using the `GetEpDevices` function on `OrtApi` which enables enumerating through all available devices. `SessionOptionsAppendExecutionProvider_V2` can then be used to explicitly append specific devices and provide custom provider options to the desired EP.

### [C#](#tab/csharp)

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

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
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

### [Python](#tab/python)

```python
# This example shows how to register a specific EP.
# Note that EPs registered by Windows ML cannot be accessed via the old "providers" option

import onnxruntime as ort

# Select a specific EP.
def add_ep_for_device(session_options, ep_name, device_type, ep_options=None):
    ep_devices = ort.get_ep_devices()
    for ep_device in ep_devices:
        if ep_device.ep_name == ep_name and ep_device.device.type == device_type:
            session_options.add_provider_for_devices([ep_device], {} if ep_options is None else ep_options)

options = ort.SessionOptions()
add_ep_for_device(options, "QNNExecutionProvider", ort.OrtHardwareDeviceType.NPU)  # example for QNN NPU
assert options.has_providers()

session = ort.InferenceSession(
    "path_to_your_model.onnx",
    sess_options=options,
)

```

---

## See also

* [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md)
* [Full code sample](https://github.com/microsoft/Build25-BRK225)