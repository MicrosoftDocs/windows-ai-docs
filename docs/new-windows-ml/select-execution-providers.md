---
title: Select execution providers using the ONNX Runtime included in Windows ML
description: Learn how to select which execution providers you want ONNX to use for hardware-optimized AI inference via Windows ML.
ms.date: 09/18/2025
ms.topic: how-to
---

# Select execution providers using the ONNX Runtime included in Windows ML

The ONNX Runtime shipped with Windows ML allows apps to configure execution providers (EPs) either based on [Device Policies](#using-device-policies-for-execution-provider-selection) or explicitly, which provides more control over provider options and which devices should be used.

We recommend starting with explicit selection of EPs so that you can have more predictability in the results. After you have this working, you can experiment with [using Device Policies](#using-device-policies-for-execution-provider-selection) to select execution providers in a natural, outcome-oriented way.

## Explicit selection of EPs

To explicitly select an EP, use the environment's `GetEpDevices` function to enumerate all available devices, and select the EP devices you want to use. Then use `AppendExecutionProvider` (C#) or `AppendExecutionProvider_V2` (C++) to append specific devices and provide custom provider options to the desired EP. You can see all of our [supported EPs here](./supported-execution-providers.md).

### [C#](#tab/csharp)

```csharp
using Microsoft.ML.OnnxRuntime;
using System;
using System.Linq;
using System.Collections.Generic;

// Assuming you've created an OrtEnv named 'ortEnv'
// 1. Enumerate devices
var epDevices = ortEnv.GetEpDevices();

// 2. Filter to your desired execution provider
var selectedEpDevices = epDevices
    .Where(d => d.EpName == "ReplaceWithExecutionProvider")
    .ToList();

if (selectedEpDevices.Count == 0)
{
    throw new InvalidOperationException("ReplaceWithExecutionProvider is not available on this system.");
}

// 3. Configure provider-specific options (varies based on EP)
// and append the EP with the correct devices (varies based on EP)
var sessionOptions = new SessionOptions();
var epOptions = new Dictionary<string,string>{ ["provider_specific_option"] = "4" };
sessionOptions.AppendExecutionProvider(ortEnv, new[] { selectedEpDevices.First() }, epOptions);
```

### [C++](#tab/cppwinrt)

```cpp
#include <iostream>
#include <iomanip>
#include <vector>
#include <stdexcept>
#include <winml/onnxruntime_cxx_api.h>

// Assuming you have an Ort::Env named 'env'
// 1. Enumerate EP devices
std::vector<Ort::ConstEpDevice> ep_devices = env.GetEpDevices();

// 2. Collect only OpenVINO devices
std::vector<Ort::ConstEpDevice> selected_ep_devices;
for (const auto& d : ep_devices) {
    if (std::string(d.EpName()) == "ReplaceWithExecutionProvider") {
        selected_ep_devices.push_back(d);
    }
}
if (selected_ep_devices.empty()) {
    throw std::runtime_error("ReplaceWithExecutionProvider is not available on this system.");
}

// 3. Configure provider-specific options (varies based on EP)
// and append the EP with the correct devices (varies based on EP)
Ort::SessionOptions session_options;
Ort::KeyValuePairs ep_options;
ep_options.Add("provider_specific_option", "4");
session_options.AppendExecutionProvider_V2(env, { selected_ep_devices.front() }, ep_options);
```

### [Python](#tab/python)

```python
# 1. Enumerate and filter EP devices
ep_devices = ort.get_ep_devices()
selected_ep_devices = [d for d in ep_devices if d.ep_name == "ReplaceWithExecutionProvider"]
if not selected_ep_devices:
    raise RuntimeError("ReplaceWithExecutionProvider is not available on this system.")

# 2. Configure provider-specific options (varies based on EP)
# and append the EP with the correct devices (varies based on EP)
options = ort.SessionOptions()
provider_options = {"provider_specific_option": "4"}
options.add_provider_for_devices([selected_ep_devices[0]], provider_options)
```

---

Browse all available EPs in the [supported EPs](./supported-execution-providers.md) docs. For more details on EP selection, see the [ONNX Runtime OrtApi documentation](https://onnxruntime.ai/docs/api/c/struct_ort_api.html).

## Using Device Policies for execution provider selection

In addition to explicitly selecting EPs, you can also use Device Policies, which are a natural, outcome-oriented way to specify how you want your AI workload to run. To do this, use `SessionOptions.SetEpSelectionPolicy`, passing in `OrtExecutionProviderDevicePolicy` values. There are a variety of values you can use for automatic selection, like `MAX_PERFORMANCE`, `PREFER_NPU`, `MAX_EFFICIENCY`, and more. See the [ONNX OrtExecutionProviderDevicePolicy docs](https://onnxruntime.ai/docs/api/c/group___global.html#gaf26ca954c79d297a31a66187dd1b4e24) for other values you can use.

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

After selecting execution providers, you're ready to **[run inference on a model using ONNX Runtime](./run-onnx-models.md)**!