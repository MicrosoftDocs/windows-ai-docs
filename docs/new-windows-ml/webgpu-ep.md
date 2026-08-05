---
title: Windows ML WebGPU execution provider (experimental)
description: Learn how to use the experimental Windows ML WebGPU execution provider to run ONNX models across a wide range of GPUs.
author: GrantMeStrength
ms.author: jken
ms.date: 08/05/2026
ms.topic: how-to
---

# Windows ML WebGPU execution provider (experimental)

> [!IMPORTANT]
> WebGPU EP is an **experimental** execution provider. It requires installing experimental NuGet packages and is not yet recommended for performance-critical production scenarios. APIs and behavior may change in future releases.
> 
> For production, the recommended approach remains using Windows ML with vendor-specific EPs for premium hardware and falling back to DirectML for broad compatibility. The WebGPU EP is advised only for experimental or special-case scenarios once thoroughly tested.

The Windows ML WebGPU execution provider (WebGPU EP) is an experimental GPU execution provider that uses the [WebGPU standard](https://www.w3.org/TR/webgpu/) (via DirectX 12) to run ONNX models across a wide range of GPUs.

> [!TIP]
> WebGPU EP is provided by ONNX Runtime. For provider-specific behavior, configuration options, and operator support, see the [ONNX Runtime WebGPU Execution Provider documentation](https://onnxruntime.ai/docs/execution-providers/WebGPU-ExecutionProvider.html).

In Windows ML, the primary and recommended GPU path is the IHV-specific execution provider for your hardware, such as MIGraphX or VitisAI for AMD, NvTensorRtRtx for NVIDIA, or OpenVINO for Intel. DirectML is available as a fallback for broad compatibility on Windows 11 24H2 and later, but is in maintenance mode. WebGPU EP is an experimental alternative intended only for the scenarios described below at this time.

WebGPU EP is compatible with almost all DirectX 12–capable GPUs.

## Licenses

WebGPU EP is governed by two licenses:

- The [WebGPU EP for Windows App SDK License](./webgpu-ep-license.md), which contains the Microsoft Software License Terms that govern distribution of the WebGPU EP.
- The [ONNX Runtime License](https://github.com/microsoft/onnxruntime/blob/main/LICENSE), which covers the ONNX Runtime components included in the WebGPU EP.

Both licenses are accepted implicitly when the respective packages are installed and used.

## When to use WebGPU EP

Consider enabling WebGPU EP in specific scenarios:

- **Broad cross-hardware coverage**: WebGPU EP can provide GPU acceleration on devices and Windows versions where IHV-specific EPs aren't available, including older hardware without a dedicated EP.

- **Unsupported Operators**: If your model contains operators or features not optimized in DirectML that cause a CPU fallback, WebGPU EP might support them. This can potentially avoid CPU fallback, improving performance.

- **Standards-based or web-first workflows**: If you are aligning with web standards (WebGPU) or bridging a browser/native scenario, WebGPU EP uses the same technology available in modern browsers. This can simplify cross-platform ML development and testing. For example, a model prototyped with ONNX Runtime Web + WebGPU in the browser can use WebGPU EP in a native Windows ML app to achieve consistent behavior.

> [!NOTE]
> DirectML is in **maintenance mode**. Microsoft continues to ship critical bug fixes and security updates, but **no new operator coverage, no new hardware-specific optimizations, and no major performance investments are planned**. Treat DirectML as a compatibility backstop for DirectX 12–capable GPUs that have no vendor EP available.
>
> Do not plan around DirectML gaining new ops, new quantization paths, or new kernel-level optimizations; those investments are happening in IHV EPs and the WebGPU EP.
> When designing new pipelines, assume DirectML's relative position will decline over time as vendor EP and WebGPU EP coverage matures, and design migration off DirectML accordingly.

## Limitations

Before enabling WebGPU EP, be aware of the following limitations:

- **Performance**: WebGPU EP is an early-stage provider and not yet recommended for performance-critical or ultra-optimized workloads. Large-scale inference pipelines that demand maximum throughput should continue to use vendor EPs or DirectML.

- **Precision support**: WebGPU EP currently supports FP32/FP16 inferencing and INT4/INT8 quantization for LLMs. Quantized non-LLM INT8 models are not supported on the WebGPU path: such models will run in higher precision or may fall back to CPU, negating performance benefits.

- **Drivers and OS**: WebGPU EP uses the Chromium Dawn engine over Direct3D 12. It requires a modern DirectX 12–capable GPU with up-to-date drivers. Using WebGPU EP on outdated drivers or legacy hardware may result in incompatibility.

- **Recommended hardware**: For a reliable experience, use recent GPUs, roughly 11th-gen Intel integrated graphics or NVIDIA Turing-class and newer. These provide the DirectX feature level (12_0+) and driver maturity that WebGPU EP expects. Older GPUs may still run but could be subject to driver or compatibility issues.

- **Model compatibility**: WebGPU EP is sensitive to model graphs and operator support. If certain operators are unsupported, ONNX Runtime will partition execution so that unsupported parts run on CPU. This can cause mixed CPU/GPU execution and unpredictable performance. Check whether your model's entire graph runs on WebGPU EP using Windows ML logs or profiling tools.

## Prerequisites

To use WebGPU EP, you need:

- **Windows 11, version 24H2 (build 26100) or later**. Windows ML execution providers are supported only on 24H2 and above.
- The experimental versions of the Windows ML NuGet packages installed in your project.

> [!NOTE]
> Install the experimental [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) NuGet package, version [`2.4.66-preview`](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.4.66-preview) or later. Check the [Windows ML release notes](./versioning.md) for the latest experimental package information.

## Enable WebGPU EP

To use WebGPU EP, enumerate the available providers, install and register WebGPU EP explicitly, bind it to a session, and unregister it when you're done. Calling `EnsureAndRegisterCertifiedAsync()` does **not** install WebGPU EP, so you must perform these steps yourself. The following example shows the full sequence; the steps are explained in [Understand the steps](#understand-the-steps) after the code.

> [!NOTE]
> The WebGPU EP is registered under the name `WebGpuExecutionProvider`. This is the value returned by `ExecutionProvider.Name` and used as the ONNX Runtime `EpName`.

### [C#](#tab/csharp)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.ML.OnnxRuntime;
using Microsoft.Windows.AI.MachineLearning;

const string WebGpuEpName = "WebGpuExecutionProvider";

// 1. Enumerate providers and find the WebGPU EP by name.
var catalog = ExecutionProviderCatalog.GetDefault();
var webGpuProvider = catalog.FindAllProviders()
    .FirstOrDefault(p => p.Name == WebGpuEpName);

if (webGpuProvider is null)
{
    Console.WriteLine("WebGPU EP is not available on this device.");
    return;
}

// 2. Install (downloads if needed) and register the EP with ONNX Runtime.
var result = await webGpuProvider.EnsureReadyAsync();
if (result.Status != ExecutionProviderReadyResultState.Success || !webGpuProvider.TryRegister())
{
    Console.WriteLine("Failed to install or register WebGPU EP.");
    return;
}

// 3. Select the WebGPU EP device and create a session bound to it.
var env = OrtEnv.Instance();
var webGpuEpDevice = env.GetEpDevices().FirstOrDefault(d => d.EpName == WebGpuEpName);
if (webGpuEpDevice is null)
{
    Console.WriteLine("WebGPU EP is not available on this device.");
    return;
}

using (var sessionOptions = new SessionOptions())
{
    sessionOptions.AppendExecutionProvider(env, new[] { webGpuEpDevice }, new Dictionary<string, string>());

    using var session = new InferenceSession("model.onnx", sessionOptions);
    // Run inference...
}

// 4. Unregister the EP library before the environment is torn down.
env.UnregisterExecutionProviderLibrary(WebGpuEpName);
```

### [C++/WinRT](#tab/cppwinrt)

```cppwinrt
#include <winrt/Microsoft.Windows.AI.MachineLearning.h>
#include <winml/onnxruntime_cxx_api.h>

using namespace winrt::Microsoft::Windows::AI::MachineLearning;

const std::string webGpuEpName = "WebGpuExecutionProvider";

// 1. Enumerate providers and find the WebGPU EP by name.
auto catalog = ExecutionProviderCatalog::GetDefault();
ExecutionProvider webGpuProvider{ nullptr };
for (auto const& provider : catalog.FindAllProviders())
{
    if (winrt::to_string(provider.Name()) == webGpuEpName)
    {
        webGpuProvider = provider;
        break;
    }
}

if (!webGpuProvider)
{
    // WebGPU EP is not available on this device.
    return;
}

// 2. Install (downloads if needed) and register the EP with ONNX Runtime.
auto result = webGpuProvider.EnsureReadyAsync().get();
if (result.Status() != ExecutionProviderReadyResultState::Success || !webGpuProvider.TryRegister())
{
    // Failed to install or register WebGPU EP.
    return;
}

// 3. Select the WebGPU EP device and create a session bound to it.
Ort::Env env;
std::vector<Ort::ConstEpDevice> selectedEpDevices;
for (auto const& epDevice : env.GetEpDevices())
{
    if (epDevice.EpName() && webGpuEpName == epDevice.EpName())
    {
        selectedEpDevices.push_back(epDevice);
        break;
    }
}

if (selectedEpDevices.empty())
{
    // WebGPU EP is not available on this device.
    return;
}

Ort::SessionOptions sessionOptions;
sessionOptions.AppendExecutionProvider_V2(env, selectedEpDevices, Ort::KeyValuePairs{});

{
    Ort::Session session(env, L"model.onnx", sessionOptions);
    // Run inference...
}

// 4. Unregister the EP library after the session is destroyed, before the
//    environment is torn down.
env.UnregisterExecutionProviderLibrary(webGpuEpName.c_str());
```

### [Python](#tab/python)

```python
import onnxruntime as ort
import windowsml as winml

WEBGPU_EP_NAME = "WebGpuExecutionProvider"

# 1. Enumerate providers and find the WebGPU EP by name.
catalog = winml.EpCatalog()
web_gpu = next(
    (p for p in catalog.find_all_providers() if p.name == WEBGPU_EP_NAME), None
)
if web_gpu is None:
    raise SystemExit("WebGPU EP is not available on this device.")

# 2. Install (downloads if needed) and register the EP with ONNX Runtime.
web_gpu.ensure_ready()
ort.register_execution_provider_library(WEBGPU_EP_NAME, web_gpu.library_path)

# 3. Select the WebGPU EP device and create a session bound to it.
ep_devices = [d for d in ort.get_ep_devices() if d.ep_name == WEBGPU_EP_NAME]
if not ep_devices:
    raise SystemExit("WebGPU EP is not available on this device.")

session_options = ort.SessionOptions()
session_options.add_provider_for_devices(ep_devices, {})

session = ort.InferenceSession("model.onnx", session_options)
# Run inference...

# 4. Unregister the EP library before the process exits.
del session
ort.unregister_execution_provider_library(WEBGPU_EP_NAME)
```

---

### Understand the steps

The preceding example separates the download, install, and registration stages that WebGPU EP requires:

- **Enumerate**: `FindAllProviders()` lists the providers available on the device. WebGPU EP appears in the list only when the experimental package is installed.
- **Download and install**: `EnsureReadyAsync()` downloads the EP package if it isn't already present and installs it locally. This is the step that `EnsureAndRegisterCertifiedAsync()` skips for WebGPU EP, so you must call it explicitly.
- **Register**: `TryRegister()` registers the installed EP library with ONNX Runtime so that a session can select it. After registration, `GetEpDevices()` returns the available EP devices. An *EP device* (`OrtEpDevice`) represents an execution provider paired with a specific hardware device it can run on. It identifies both the provider (through its `EpName`) and the underlying hardware device, rather than a hardware device on its own. Select the EP device whose `EpName` matches WebGPU EP, then bind it to your session options.
- **Unregister**: `UnregisterExecutionProviderLibrary()` removes the registration before the environment is torn down.

Provider specific options can be found in the [ONNX WebGPU Execution Provider documentation](https://onnxruntime.ai/docs/execution-providers/WebGPU-ExecutionProvider.html#configuration-options). After you have the WebGPU EP working, benchmark its performance and behavior on your target hardware. Use your benchmarking data to guide adoption decisions, and stay updated as the WebGPU EP adds new improvements over time.

## Report issues

File all issues about WebGPU EP in the [Windows ML GitHub repository](https://github.com/microsoft/WindowsML/issues). If the Windows ML team determines that an issue is specific to ONNX Runtime, they may transfer it to the [ONNX Runtime GitHub repository](https://github.com/microsoft/onnxruntime/issues).

## See also

- [Windows ML execution providers](./supported-execution-providers.md)
- [Accelerate AI models](./accelerate-ai-models.md)
- [Select execution providers](./select-execution-providers.md)
- [Capture logs](./logs.md)
