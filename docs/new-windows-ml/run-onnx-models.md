---
title: Run ONNX models with Windows ML
description: Learn how to use Windows Machine Learning (ML) to run local AI ONNX models in your Windows apps.
ms.date: 05/20/2025
ms.topic: how-to
---

# Run ONNX models with Windows ML

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

With the Windows Machine Learning (ML) types in the **Microsoft.Windows.AI.MachineLearning** namespace, you can run ONNX models locally in your Windows apps without having to manually manage the underlying execution provider (EP) packages. The APIs handle downloading, updating, and initializing EPs; which you can then continue to use with **Microsoft.Windows.AI.MachineLearning**, and/or with the [ONNX Runtime](https://onnxruntime.ai/).

## Prerequisites

* Windows 11 PC running version 24H2 (build 26100) or greater.

In addition to the above, there are language-specific prerequisites depending on what language your app is written in.

### [C#](#tab/csharp)

* .NET 8 or greater
* Targeting TFM `windows10.0.26100` or greater

### [C++](#tab/cppwinrt)

C++20 or later.

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Install the WinML runtime package and NuGet package

The runtime package is distributed via the Microsoft Store. Run the following command in Windows Terminal to install it (the identifier is the Store's Catalog ID for the runtime package):

```console
winget install --id 9MVL55DVGWWW
```

Then follow the steps below based on the programming language of your application.

### [C#](#tab/csharp)

In your .NET project, add the [**Microsoft.Windows.AI.MachineLearning** NuGet package](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) (make sure to include prerelease packages if using the NuGet Package Manager).

```dotnetcli
dotnet add package Microsoft.Windows.AI.MachineLearning --prerelease
```

And then import the namespaces in your code.

```csharp
using Microsoft.ML.OnnxRuntime;
using Microsoft.Windows.AI.MachineLearning;
```

### [C++](#tab/cppwinrt)

In your Visual Studio project, use the NuGet Package Manager to search for and add the [**Microsoft.Windows.AI.MachineLearning** NuGet package](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) to your project (make sure to include prerelease pacakges in your search).

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
// First we create a new instance of EnvironmentCreationOptions
EnvironmentCreationOptions envOptions = new()
{
    logId = "WinMLDemo", // Use an ID of your own choice
    logLevel = OrtLoggingLevel.ORT_LOGGING_LEVEL_ERROR
};

// And then use that to create the ORT environment
using var ortEnv = OrtEnv.CreateInstanceWithOptions(ref envOptions);

// Then, initialize Windows ML infrastructure
Infrastructure infrastructure = new();

// Ensure the latest execution providers are available (downloads them if they aren't)
await infrastructure.DownloadPackagesAsync();

// And register the EPs with ONNX Runtime
await infrastructure.RegisterExecutionProviderLibrariesAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
// First we need to create an ORT environment
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "WinMLDemo"); // Use an ID of your own choice

// Then, initialize Windows ML infrastructure
winrt::Microsoft.Windows.AI.MachineLearning::Infrastructure infrastructure{};

// Ensure the latest execution providers are available (downloads them if they aren't)
co_await infrastructure.DownloadPackagesAsync();

// And register the EPs with ONNX Runtime
co_await infrastructure.RegisterExecutionProviderLibrariesAsync();
```

### [Python](#tab/python)

For Python, the EPs are automatically downloaded and registered as part of the first import. You don't need to do anything additional.

---

## Step 3: Configure the execution providers

The ONNX Runtime allow apps to configure execution providers (EPs) based on [Device Policies](#using-device-policies-for-execution-provider-selection), or explicitly, which allows for more control over provider options and which devices should be used.

We recommend starting with explicit selection of EPs so that you can have more predictibility in the results. After you have this working, you can experiment with [using Device Policies](#using-device-policies-for-execution-provider-selection) to select execution providers in a natural, outcome-oriented way.

To explicitly select one or more EPs, you will use the `GetEpDevices` function on `OrtApi`, which enables enumerating through all available devices. `SessionOptionsAppendExecutionProvider_V2` can then be used to explicitly append specific devices and provide custom provider options to the desired EP.

### [C#](#tab/csharp)

```csharp
using Microsoft.ML.OnnxRuntime;

// Get all available EP devices from the environment
var epDevices = ortEnv.GetEpDevices();

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
            sessionOptions.AppendExecutionProvider(ortEnv, devices, epOptions);
            Console.WriteLine($"Successfully added {epName} EP");
            break;

        case "OpenVINOExecutionProvider":
            // Configure threading for OpenVINO EP, pick the first device found
            epOptions["num_of_threads"] = "4";
            sessionOptions.AppendExecutionProvider(ortEnv, [devices.First()], epOptions);
            Console.WriteLine($"Successfully added {epName} EP (first device only)");
            break;

        case "QNNExecutionProvider":
            // Configure performance mode for QNN EP
            epOptions["htp_performance_mode"] = "high_performance";
            sessionOptions.AppendExecutionProvider(ortEnv, devices, epOptions);
            Console.WriteLine($"Successfully added {epName} EP");
            break;

        default:
            Console.WriteLine($"Skipping EP: {epName}");
            break;
    }
}

```

### [C++](#tab/cppwinrt)

```cppwinrt
#include <win_onnxruntime_cxx_api.h>

// Get all available EP devices from the environment
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

For more details see the [ONNX Runtime OrtApi documentation](https://onnxruntime.ai/docs/api/c/struct_ort_api.html). To learn about the versioning strategy around EPs, see the [versioning of execution providers documentation](./versioning.md).

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

#### [C++](#tab/cppwinrt)

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

## Step 5: Run model inference

Now that the model is compiled for the local hardware on the device, we can create an inference session and inference the model.

#### [C#](#tab/csharp)

```csharp
// Create inference session using compiled model
using InferenceSession session = new(compiledModelPath, sessionOptions);
```

#### [C++](#tab/cppwinrt)

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

## Using Device Policies for execution provider selection

In addition to explicitly selecting EPs, you can also use Device Policies, which are a natural, outcome-oriented way for you to specify how you want your AI workload to be run. To do this, you'll use the `SessionOptions.SetEpSelectionPolicy` function on the `OrtApi`, passing in the `OrtExecutionProviderDevicePolicy` values. There are a variety of values you can use for automatic selection, like `MAX_PERFORMANCE`, `PREFER_NPU`, `MAX_EFFICIENCY`, and more. See the [ONNX OrtExecutionProviderDevicePolicy docs](https://onnxruntime.ai/docs/api/c/group___global.html#gaf26ca954c79d297a31a66187dd1b4e24) for other values you can use.

### [C#](#tab/csharp)

```csharp
// Configure the session to select an EP and device for MAX_EFFICIENCY which typically
// will choose an NPU if available with a CPU fallback.
var sessionOptions = new SessionOptions();
sessionOptions.SetEpSelectionPolicy(ExecutionProviderDevicePolicy.MAX_EFFICIENCY);
```

### [C++](#tab/cppwinrt)

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

## Providing feedback about Windows ML

We would love to hear your feedback about using Windows ML! If you run into any issues, please use the Feedback Hub app on Windows to report your issue.

Feedback should be submitted under the ***Developer Platform -> Windows Machine Learning*** category.

## See also

* [Windows ML APIs in Microsoft.Windows.AI.MachineLearning](./api-reference.md)
