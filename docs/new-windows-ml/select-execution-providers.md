---
title: Select exectution providers in ONNX Runtime
description: Learn how to select which execution providers you want ONNX to use for hardware-optimized AI inference via Windows ML.
ms.date: 08/08/2025
ms.topic: how-to
---

# Select execution providers in ONNX Runtime

The ONNX Runtime shipped with Windows ML allow apps to configure execution providers (EPs) based on [Device Policies](#using-device-policies-for-execution-provider-selection), or explicitly, which allows for more control over provider options and which devices should be used.

We recommend starting with explicit selection of EPs so that you can have more predictibility in the results. After you have this working, you can experiment with [using Device Policies](#using-device-policies-for-execution-provider-selection) to select execution providers in a natural, outcome-oriented way.

## Explicit selection of EPs

To explicitly select one or more EPs, you will use the `GetEpDevices` function on `OrtApi`, which enables enumerating through all available devices. `SessionOptionsAppendExecutionProvider_V2` can then be used to explicitly append specific devices and provide custom provider options to the desired EP.

### [C#](#tab/csharp)

```csharp
using Microsoft.ML.OnnxRuntime;

// Get all available EP devices from the environment
var epDevices = ortEnv.GetEpDevices();

// Group devices by EP name - this allows passing multiple devices of the same type
// to an EP, enabling the EP to choose the optimal configuration or device combination
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

For more details see the [ONNX Runtime OrtApi documentation](https://onnxruntime.ai/docs/api/c/struct_ort_api.html).

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

## Next steps

After selecting execution providers, you're ready to **[inference a model using ONNX Runtime](./run-onnx-models.md)**!