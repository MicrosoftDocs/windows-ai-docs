---
title: Compile and cache ONNX models in Windows ML
description: Learn how to compile, cache, and validate ONNX models for Windows ML execution providers before reusing device-specific artifacts.
author: Juan-
ms.author: jusepulv
ms.topic: how-to
ms.date: 09/24/2026
---

# Compile and cache ONNX models in Windows ML

Compilation is the step that turns your ONNX model into the hardware-specific binary an execution provider (EP) actually runs. On NPUs and GPUs that step can take anywhere from a few seconds to several minutes for large models — and if your app doesn't cache the result, users pay that cost every time your app starts.

This page explains what compilation involves, when to pre-compile, and how to validate a cached compiled model before you reuse it.

## Prerequisites

The compiled-model compatibility APIs require Windows ML 2.3 or later. In upstream ONNX Runtime, `GetModelCompatibilityForEpDevices()` is available in version 1.23 and `GetCompatibilityInfoFromModel()` is available in version 1.24.

## Two kinds of compilation

"Compilation" can mean two different things. Separating them makes the rest of this page easier to reason about:

- **Graph optimization** — Fusion, constant folding, and layout work that ONNX Runtime performs at session creation. It's cheap, EP-agnostic, and runs every time you create a session.
- **EP compilation** — Conversion of the ONNX graph into an EP's own format, followed by compilation down to a hardware-specific binary. Hardware EPs (QNN, OpenVINO, VitisAI, NvTensorRtRtx, MIGraphX) do this, and it's the expensive step. On NPUs it can take tens of seconds to minutes for larger models.

Windows ML avoids re-paying EP compilation by using ONNX Runtime's [EPContext](https://onnxruntime.ai/docs/execution-providers/EP-Context-Design.html) mechanism. The first compile serializes a hardware-ready binary into a `*_ctx.onnx` model (or a sidecar `.bin`); later sessions load the pre-built binary and skip conversion entirely.

## Why pre-compile?

**Windows ML highly recommends pre-compilation when using execution providers.** Pre-compiling turns a multi-second — sometimes multi-minute — cold start into a one-time cost, and gives your app:

- **Fast cold starts** — subsequent launches skip graph conversion and hardware compilation.
- **Predictable behavior** — the platform supports checking whether a compiled model is optimal for the devices your app selects before you create an inference session.
- **Lower CPU and battery use** — you stop recompiling the same graph on every launch.

Without pre-compilation, your app re-runs EP conversion and compilation on every session creation, and users feel the delay each time.

## When to pre-compile

Pre-compile whenever your app targets a hardware EP. Two timing options are available:

| Strategy | Best for | Trade-offs |
| --- | --- | --- |
| **Ahead-of-time (AOT)** — compile at build or install time and ship the compiled artifact. | Controlled hardware and driver fleets, or per-device installers. | Requires cross-compilation tooling for targets your build machine can't run. Validate the artifact on the target device and provide a recompilation fallback. |
| **Compile on first run** *(recommended for broad distribution)* — check for a compiled artifact, compile it if missing, then cache and reuse it. | Store apps and general consumer distribution across a diverse hardware base. | Users pay the compile cost once, on first launch. |

The [Windows ML walkthrough](tutorial.md#ep-compilation) demonstrates the basic compile-on-first-run mechanics by checking whether a compiled artifact exists. It isn't a complete cache-management example. Before you reuse an existing compiled model in an app, apply the compatibility check described in this article. For the compilation API in context, see [Compile models](#compile-models).

## Validate a compiled model before reuse

A compiled model can depend on the EP and device configuration that produced it. The EP determines whether a cached model remains compatible and optimal for a set of devices.

During compilation, an EP can generate a null-terminated UTF-8 compatibility string that ONNX Runtime writes to the compiled model's metadata properties. The string's format and contents are defined by the EP. Your app shouldn't parse or interpret it.

Your app passes the string and the target EP devices to `GetModelCompatibilityForEpDevices()`. ONNX Runtime calls the EP factory's validation implementation and returns an `OrtCompiledModelCompatibility` status. For the source contracts, see [`GetModelCompatibilityForEpDevices()`](https://github.com/microsoft/onnxruntime/blob/main/include/onnxruntime/core/session/onnxruntime_c_api.h) and [`ValidateCompiledModelCompatibilityInfo()`](https://github.com/microsoft/onnxruntime/blob/main/include/onnxruntime/core/session/onnxruntime_ep_c_api.h) in the ONNX Runtime repository.

### Compatibility-check scenarios

You can use the same validation API in two scenarios:

- **Validate a model that is already on the device.** Call `GetCompatibilityInfoFromModel()` to extract the string from a compiled model file. If the model is already in memory, call `GetCompatibilityInfoFromModelBytes()`. Then pass the string to `GetModelCompatibilityForEpDevices()`.
- **Validate before downloading a compiled model.** Publish the opaque string as lightweight metadata alongside the compiled model. Your app can retrieve the string first, validate it with `GetModelCompatibilityForEpDevices()`, and download the potentially large model only when the returned status meets your cache or deployment policy.

Keep remote compatibility metadata paired with the exact compiled artifact that produced it. If the artifact changes, update the associated compatibility string.

In either scenario:

1. Call `GetEpDevices()` to enumerate the available EP devices, then apply the same EP or device policy that you use to configure the inference session.
2. Obtain the compatibility string from the local model or from metadata associated with a remote model.
3. Pass the string and the matching device group to `GetModelCompatibilityForEpDevices()`.
4. Apply your cache or download policy to the returned status. The Windows ML samples use a conservative policy and accept the compiled model only for `EP_SUPPORTED_OPTIMAL`.

`GetCompatibilityInfoFromModel()` parses the full ONNX `ModelProto` from disk. Use the remote-metadata pattern when you need to make a compatibility decision before downloading the model itself.

The APIs return an `OrtCompiledModelCompatibility` value:

| Status | Meaning | Recommended action |
| --- | --- | --- |
| `EP_SUPPORTED_OPTIMAL` | The compiled model is supported and optimal for the EP devices. | Reuse the compiled model. |
| `EP_SUPPORTED_PREFER_RECOMPILATION` | The compiled model is supported, but the EP recommends recompilation for better performance. | The model can run, but an optimal-only cache policy recompiles it. |
| `EP_UNSUPPORTED` | The compiled model isn't supported by the current EP devices. | Don't load the compiled model. Recompile or use the original model. |
| `EP_NOT_APPLICABLE` | The EP didn't provide a compatibility determination for the supplied devices. This is also the default when the EP doesn't implement compatibility validation. | Don't treat this status as evidence that the cache is compatible. Use the original model unless your app has another validation policy. |

Under an optimal-only cache policy, treat missing compatibility metadata for the preferred EP as a cache miss. If compatibility evaluation returns an error, use the original model for that run and don't promote a newly compiled artifact because your app can't validate it.

Events that may require a new cache entry include:

- A new EP is installed.
- The GPU or NPU driver is updated.
- The user's hardware changes.
- The source ONNX model changes.
- Your app changes the ONNX Runtime, EP, or compilation settings used to produce the artifact.

The compatibility APIs validate an existing compiled artifact against EP devices; they don't determine whether the artifact was produced from the current source model. Include a source-model version or hash in the cache key, and invalidate the cache when compilation inputs change.

If your app is distributed across a range of devices, store compiled artifacts in a local cache location so each device produces and reuses its own binary.

### Basic compatibility check

The following helper functions check a compiled model for one EP. Before calling a helper, apply the same EP or device policy that you use to configure the inference session. The device collection must be non-empty and contain devices from the same EP.

#### [C#](#tab/csharp-compatibility)

```csharp
static bool IsCompiledModelOptimal(
    OrtEnv ortEnv,
    string compiledModelPath,
    string epName,
    IReadOnlyList<OrtEpDevice> epDevices)
{
    string compatibilityInfo =
        ortEnv.GetCompatibilityInfoFromModel(compiledModelPath, epName);

    return !string.IsNullOrWhiteSpace(compatibilityInfo) &&
        ortEnv.GetModelCompatibilityForEpDevices(
            epDevices,
            compatibilityInfo) ==
        OrtCompiledModelCompatibility.EP_SUPPORTED_OPTIMAL;
}
```

#### [C++](#tab/cpp-compatibility)

```cpp
#include <onnxruntime_cxx_api.h>

bool IsCompiledModelOptimal(
    const ORTCHAR_T* compiledModelPath,
    const char* epName,
    const std::vector<Ort::ConstEpDevice>& epDevices)
{
    Ort::AllocatorWithDefaultOptions allocator;
    auto compatibilityInfo = Ort::GetCompatibilityInfoFromModelAllocated(
        compiledModelPath,
        epName,
        allocator);

    return compatibilityInfo &&
        Ort::GetModelCompatibilityForEpDevices(
            epDevices,
            compatibilityInfo.get()) ==
        OrtCompiledModelCompatibility_EP_SUPPORTED_OPTIMAL;
}
```

#### [Python](#tab/python-compatibility)

```python
import onnxruntime as ort


def get_compatibility_api(name: str):
    api = getattr(ort, name, None)
    if api is not None:
        return api

    from onnxruntime.capi import onnxruntime_pybind11_state

    return getattr(onnxruntime_pybind11_state, name)


def is_compiled_model_optimal(
    compiled_model_path: str, ep_name: str, ep_devices: list
) -> bool:
    get_compatibility_info = get_compatibility_api(
        "get_compatibility_info_from_model"
    )
    get_compatibility = get_compatibility_api(
        "get_model_compatibility_for_ep_devices"
    )
    compatibility_status = get_compatibility_api(
        "OrtCompiledModelCompatibility"
    )

    compatibility_info = get_compatibility_info(
        compiled_model_path, ep_name
    )

    return (
        compatibility_info is not None
        and get_compatibility(ep_devices, compatibility_info)
        == compatibility_status.EP_SUPPORTED_OPTIMAL
    )
```

---

Use the check before session creation:

- If the result meets your app's compatibility policy, use the compiled model.
- Otherwise, use or recompile the original model.

The Windows ML samples use a conservative policy and accept only `EP_SUPPORTED_OPTIMAL`. Your app determines how to store, refresh, and replace compiled models.

## Compile models

Before using an ONNX model in an inference session, it often must be compiled into an optimized representation that can be executed efficiently on the device's underlying hardware.

As of ONNX Runtime 1.22, there are new APIs that better encapsulate the compilation steps. More details are available in the ONNX Runtime compile documentation (see [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html)).

#### [C#](#tab/csharp)

```csharp
// Prepare compilation options
OrtModelCompilationOptions compileOptions = new(sessionOptions);
compileOptions.SetInputModelPath(modelPath);
compileOptions.SetOutputModelPath(compiledModelPath);

// Compile the model
compileOptions.CompileModel();
```

#### [C++](#tab/cpp)

```cpp
const OrtCompileApi* compileApi = ortApi.GetCompileApi();

// Prepare compilation options
OrtModelCompilationOptions* compileOptions = nullptr;
OrtStatus* status = compileApi->CreateModelCompilationOptionsFromSessionOptions(env, sessionOptions, &compileOptions);
status = compileApi->ModelCompilationOptions_SetInputModelPath(compileOptions, modelPath.c_str());
status = compileApi->ModelCompilationOptions_SetOutputModelPath(compileOptions, compiledModelPath.c_str());

// Compile the model
status = compileApi->CompileModel(env, compileOptions);

// Clean up
compileApi->ReleaseModelCompilationOptions(compileOptions);
```

#### [Python](#tab/python)

```python
import os

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

> [!NOTE]
> Compilation can take several minutes to complete. So that any UI remains responsive, consider doing this as a background operation in your application or alert the user a model is being prepared.

## See also

- [Accelerate AI models with Windows ML](accelerate-ai-models.md) — overview of execution providers and hardware acceleration
- [Windows ML walkthrough](tutorial.md) — end-to-end example that includes compile-on-first-run
- [Run ONNX models](run-onnx-models.md#compile-models) — the `OrtModelCompilationOptions` API in context
- [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML) — C++, C#, and Python examples that validate compiled-model compatibility
- [Windows ML execution providers](supported-execution-providers.md) — versions of the EPs your app can target
- [ONNX Runtime C API](https://github.com/microsoft/onnxruntime/blob/main/include/onnxruntime/core/session/onnxruntime_c_api.h) — compatibility status and extraction API contracts
- [EP Context Design](https://onnxruntime.ai/docs/execution-providers/EP-Context-Design.html) *(for EP authors)* — the ONNX Runtime design behind the `*_ctx.onnx` artifact
