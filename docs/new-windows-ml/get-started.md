---
title: Get started with Windows ML
description: Learn how to use Windows ML to download and register AI execution providers for hardware-optimized inference.
ms.date: 08/08/2025
ms.topic: how-to
---

# Get started with Windows ML

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

This guide shows you how to install and use Windows ML to discover, download, and register execution providers (EPs) for use with the ONNX Runtime shipped with Windows ML. Windows ML handles the complexity of package management and hardware selection, automatically downloading the latest execution providers compatible with your device's hardware.

If you're not already familiar with the ONNX Runtime, we suggest reading the [ONNX Runtime docs](https://onnxruntime.ai/docs/). In short, Windows ML provides a shared Windows-wide copy of the ONNX Runtime, plus the ability to dynamically download execution providers (EPs).

## Prerequisites

* Windows 11 PC running version 24H2 (build 26100) or greater
* Language-specific prerequisites seen below

### [C#](#tab/csharp)

* .NET 6 or greater
* Targeting a Windows 10-specific TFM like `net6.0-windows10.0.19041.0` or greater

### [C++](#tab/cppwinrt)

C++20 or later.

### [Python](#tab/python)

Python versions 3.10 to 3.13, on x64 and ARM64 devices.

---

## Step 1: Install the Windows App SDK and Windows ML NuGet packages

Follow the steps below based on the programming language of your application.

### [C#](#tab/csharp)

In your .NET project, add the latest [**Microsoft.WindowsAppSDK** experimental NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK), which includes Windows ML as a dependency. Make sure you install the latest experimental version, as the release versions don't contain Windows ML yet:

```dotnetcli
dotnet add package Microsoft.WindowsAppSDK --prerelease
```

Alternatively, you can reference both Windows ML and WindowsAppSDK Runtime packages directly:

```dotnetcli
dotnet add package Microsoft.WindowsAppSDK.ML --prerelease
dotnet add package Microsoft.WindowsAppSDK.Runtime --prerelease
```

And then import the namespaces in your code:

```csharp
using Microsoft.ML.OnnxRuntime;
using Microsoft.Windows.AI.MachineLearning;
```

### [C++](#tab/cppwinrt)

In your Visual Studio project, use the NuGet Package Manager to search for and add the [**Microsoft.WindowsAppSDK** experimental NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK) to your project (make sure to include prerelease packages in your search and install the latest experimental version, as the release versions don't contain Windows ML yet).

Alternatively, you can reference both Windows ML and WindowsAppSDK Runtime packages directly:
- Microsoft.WindowsAppSDK.ML
- Microsoft.WindowsAppSDK.Runtime

And then add the necessary header files to your code:

```cppwinrt
#include <winrt/Microsoft.Windows.AI.MachineLearning.h>
#include <win_onnxruntime_cxx_api.h>
```


### [Python](#tab/python)

The python binding leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project for the Windows App SDK projection. Those packages are hosted in a private index for the 1.8 experimental 4 release. They will upstream to pywinrt and publish to PyPI in future versions. You will also need to install a special onnxruntime package with the auto-ep feature enabled. This feature will be available in a future onnxruntime release as well. For now, please install those packages with the following requirements file:

```
--index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple
--extra-index-url https://pypi.org/simple
onnxruntime-winml==1.22.0.post2
winrt-runtime==3.2.1
winrt-Windows.Foundation==3.2.1
winrt-Windows.Foundation.Collections==3.2.1
winui3-Microsoft.Windows.AI.MachineLearning==1!1.8.250702007.dev4
winui3-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap==1!1.8.250702007.dev4
```

---

## Step 2: Download and register EPs

The simplest way to get started is to let Windows ML automatically discover, download, and register all compatible execution providers:

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

// Get the default ExecutionProviderCatalog
var catalog = ExecutionProviderCatalog.GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
// This downloads any necessary components and registers them
await catalog.EnsureAndRegisterAllAsync();
```

### [C++](#tab/cppwinrt)

```cppwinrt
// First we need to create an ORT environment
Ort::Env env(ORT_LOGGING_LEVEL_ERROR, "WinMLDemo"); // Use an ID of your own choice

// Get the default ExecutionProviderCatalog
winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog catalog =
    winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();

// Ensure and register all compatible execution providers with ONNX Runtime
catalog.EnsureAndRegisterAllAsync().get();
```

### [Python](#tab/python)

```python
# Known issue: import winrt.runtime will cause the TensorRTRTX execution provider to fail registration.
# As a workaround, please run pywinrt related code in a separate thread.

# winml.py
import json

def _get_ep_paths() -> dict[str, str]:
    from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
        InitializeOptions,
        initialize
    )
    import winui3.microsoft.windows.ai.machinelearning as winml
    eps = {}
    with initialize(options = InitializeOptions.ON_NO_MATCH_SHOW_UI):
        catalog = winml.ExecutionProviderCatalog.get_default()
        providers = catalog.find_all_providers()
        for provider in providers:
            provider.ensure_ready_async().get()
            eps[provider.name] = provider.library_path
            # DO NOT call provider.try_register in python. That will register to the native env.
    return eps

if __name__ == "__main__":
    eps = _get_ep_paths()
    print(json.dumps(eps))

# In your application code
import subprocess
import json
import sys
from pathlib import Path
import onnxruntime as ort

def register_execution_providers():
    worker_script = str(Path(__file__).parent / 'winml.py')
    result = subprocess.check_output([sys.executable, worker_script], text=True)
    paths = json.loads(result)
    for item in paths.items():
        ort.register_execution_provider_library(item[0], item[1])
    _ep_registered = True

register_execution_providers()
```

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
# This example shows how to use a specific EP.
# Note that EPs registered with ort.register_execution_provider_library 
# cannot be accessed via the old "providers" option

# Assuming you have already run the registration code.

ep_devices = ort.get_ep_devices()
ep_device_map = {}
for device in ep_devices:
    ep_name = device.ep_name
    if ep_name not in ep_device_map:
        ep_device_map[ep_name] = []
    ep_device_map[ep_name].append(device)

for name, devices in ep_device_map.items():
    print(f"Execution Provider: {name}")
    for device in devices:
        device_type = ort.OrtHardwareDeviceType(device.device.type).name
        print(f" | Vendor: {device.ep_vendor:<16} | Device Type: {device_type:<8}")

session_options = ort.SessionOptions()
for name, devices in ep_device_map.items():
    if name == "VitisAIExecutionProvider":
        session_options.add_provider_for_devices(devices, {})
    elif name == "OpenVINOExecutionProvider":
        session_options.add_provider_for_devices(
            [devices[0]], {"num_of_threads": "4"})
    elif name == "QNNExecutionProvider":
        session_options.add_provider_for_devices(
            devices, {"htp_performance_mode": "high_performance"})
    else:
        print(f"Skipping EP: {name}")
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
if not os.path.exists(output_model_path):
    # For some EP, there might not be a compilation output.
    # In that case, use the original model directly.
    output_model_path = input_model_path
```

---

## Step 5: Run model inference

Now that the model is compiled for the local hardware on the device, we can create an inference session and inference the model.

> [!NOTE]
> Inferencing is different for every model. We're only creating the inferencing session below. You'll need to pass the input and output paramaters accordingly based on your model. For more info, see the [ONNX Runtime docs](https://onnxruntime.ai/docs/).

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

## Discovering available execution providers

Before downloading, you can discover what execution providers are compatible with your hardware:

### [C#](#tab/csharp)

```csharp
var catalog = ExecutionProviderCatalog.GetDefault();
var providers = catalog.FindAllProviders();

foreach (var provider in providers)
{
    Console.WriteLine($"Provider: {provider.Name}");
    Console.WriteLine($"  Device Type: {provider.DeviceType}");
    Console.WriteLine($"  Ready: {provider.IsReady}");
    Console.WriteLine();
}
```

### [C++](#tab/cppwinrt)

```cppwinrt
auto catalog = winrt::Microsoft::Windows::AI::MachineLearning::ExecutionProviderCatalog::GetDefault();
auto providers = catalog.FindAllProviders();

for (const auto& provider : providers)
{
    std::wcout << L"Provider: " << provider.Name().c_str() << L"\n";
    std::wcout << L"  Device Type: " << static_cast<int>(provider.DeviceType()) << L"\n";
    std::wcout << L"  Ready: " << (provider.IsReady() ? L"Yes" : L"No") << L"\n\n";
}
```

### [Python](#tab/python)

```python
with initialize(options=InitializeOptions.ON_NO_MATCH_SHOW_UI):
    catalog = winml.ExecutionProviderCatalog.get_default()
    providers = catalog.find_all_providers()
    
    for provider in providers:
        print(f"Provider: {provider.name}")
        print(f"  Device Type: {provider.device_type}")
        print(f"  Ready: {provider.is_ready}")
        print()
```

---



## Next steps

After initializing execution providers, you're ready to:

1. **Configure execution providers** - Learn how to select and configure specific execution providers by reading the [ONNX Runtime execution provider documentation](https://onnxruntime.ai/docs/execution-providers/)
2. **Run model inference** - See the complete workflow in [Run ONNX models with Windows ML](./run-onnx-models.md)
3. **Explore the API reference** - Review all available methods in [Windows ML APIs](./api-reference.md)

## See also

* [Run ONNX models with Windows ML](./run-onnx-models.md)
* [Windows ML APIs](./api-reference.md)
* [ONNX Runtime execution providers](https://onnxruntime.ai/docs/execution-providers/)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)
