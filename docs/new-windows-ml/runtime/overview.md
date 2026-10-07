---
title: Windows ML Runtime API overview
description: Learn about the experimental Windows ML Runtime API for native ONNX model execution on Windows.
ms.date: 09/28/2026
ms.topic: overview
---

# Windows ML Runtime API overview

> [!IMPORTANT]
> The Windows ML Runtime API is currently experimental and **not supported** for use in production environments. Apps trying out this API should not be published to the Microsoft Store.

> [!IMPORTANT]
> The Windows ML Runtime API is currently available in C++ and Python; **C# is currently not supported.** Let us know if you're interested in using these APIs from C# by [posting an issue in the Windows ML GitHub repo](https://github.com/microsoft/WindowsML/issues)!

The Windows ML Runtime API is a native C/C++ local AI inferencing framework built specifically for Windows. With the Windows ML Runtime API, you can load a model, choose a CPU, GPU, or NPU execution target (including a specific DXCore adapter or D3D12 device), bind tensors to that target, compose model and processor stages into a pipeline, compile the pipeline, and run it to produce output tensors.

The optional Text Generation and Speech Recognition APIs compose these same
Runtime objects into higher-level workflows over a model your app supplies:
see [Generate text with your own language model using Windows
ML](./text-generation.md) and [Recognize speech with your own Whisper model
using Windows ML](./speech-recognition.md).

## Runtime API vs ONNX Runtime

If your app only targets Windows (and you're willing to use APIs that are currently experimental), use the Runtime API. It gives explicit
control over hardware selection (including a specific DXCore adapter or D3D12
device), tensor residency, multi-stage pipeline placement, and compilation.

Otherwise, if your app needs to run on other platforms, or already has working
cross-platform ONNX Runtime session code, use the [ONNX Runtime APIs included
in Windows ML](../use-onnx-apis.md) instead.

## Core object model

| Concept | Primary API | Description |
|---------|-------------|-------------|
| Runtime | `IWinMLRuntime` | Interface returned by `WinMLCreateRuntime`; loads model files, creates execution targets, and creates pipeline builders. |
| Model | `IWinMLModel` | Immutable, opaque model artifact loaded from a file or buffer. Reflect its declared schema through `IWinMLModelSchema`. |
| Execution target | `IWinMLExecutionTarget` | CPU, GPU, or NPU placement token used to build stages and create tensors. |
| Pipeline builder | `IWinMLPipelineBuilder` | Adds model and processor stages, connects them, and builds a prepared pipeline. |
| Pipeline | `IWinMLPipeline` | Prepared, immediately runnable graph that executes bound tensors and produces output tensors. |
| Stage | `IWinMLStage` | A step in the pipeline that owns positional input and output bindings and its resolved execution target. |
| Tensor | `IWinMLTensor` | Typed multi-dimensional data bound to stage inputs and outputs. |
| Text generation / speech recognition | Windows ML Text Generation and Speech Recognition APIs | Higher-level workflow composed over caller-owned Runtime objects, with typed sessions, streams, and results. |

## Choose a backend and execution target

`LoadModelFromFile` selects the backend from the model artifact's file extension.
Your app uses the same Runtime objects — model, execution target, pipeline
builder, stages, bindings, and `Run` — no matter which backend loads the file.

| Model file | Backend | Hardware acceleration |
|---|---|---|
| `.onnx`, `.ort` | ONNX Runtime | [Execution providers (EPs)](../supported-execution-providers.md) that Windows installs and keeps up to date through the Windows ML EP catalog |
| `.gguf` | llama.cpp | An app-local CPU module, plus an optional CUDA module for NVIDIA GPUs that you deploy alongside your app. CPU and CUDA are the only llama.cpp backends Windows ML validates for this release. |

An execution target represents a hardware class — CPU, GPU, or NPU — created
with `IWinMLRuntime`. Reuse a target to build stages and create tensors:
reusing one target expresses same-target affinity, while using distinct
targets makes cross-target transfer boundaries explicit. When the pipeline
builder's `Build` method runs, the runtime places each stage on the execution
backend for its target and commits target and backend compatibility.

For a hardware class without enumerating devices yourself, call
`CreateExecutionTarget` with a `WINML_EXECUTION_TARGET_KIND` and a
`WINML_EXECUTION_TARGET_PREFERENCE` that ranks candidates of that class, such as
an integrated versus a discrete GPU. Treat `ERROR_NOT_FOUND` and
`ERROR_NOT_SUPPORTED` alike and fall back to another class:

```cpp
ComPtr<IWinMLExecutionTarget> target;
HRESULT hr = runtime->CreateExecutionTarget(
    WINML_EXECUTION_TARGET_KIND_NPU,
    WINML_EXECUTION_TARGET_PREFERENCE_EFFICIENCY,
    target.GetAddressOf());
if (hr == HRESULT_FROM_WIN32(ERROR_NOT_FOUND) ||
    hr == HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED))
{
    THROW_IF_FAILED(runtime->CreateExecutionTarget(
        WINML_EXECUTION_TARGET_KIND_GPU,
        WINML_EXECUTION_TARGET_PREFERENCE_PERFORMANCE,
        target.GetAddressOf()));
}
else
{
    THROW_IF_FAILED(hr);
}
```

`CreateCpuExecutionTarget` is equivalent to requesting the CPU kind.
`CreateExecutionTargetFromAdapter` and `CreateExecutionTargetFromD3D12` bind a
target to a specific DXCore adapter or D3D12 device instead of letting the
Runtime pick one. For ONNX models that depend on an installed [execution
provider (EP)](../supported-execution-providers.md), an optional ONNX Runtime
compatibility surface lets you pin a provider you have already registered.
Each backend resolves these targets differently — for example, the llama.cpp
backend used by `.gguf` models requires at least partial GPU offload to honor a
GPU target, and doesn't support NPU targets or provider pinning. For the full
target-resolution matrix per backend, and for a walkthrough of running a GGUF
model, see [Windows ML Runtime API
concepts](./concepts.md#execution-targets).

## Next steps

- [Set up a C++ project for the Windows ML Runtime API](./get-started.md)
- [Windows ML Runtime API concepts](./concepts.md)
- [Generate text with your own language model using Windows ML](./text-generation.md)
- [Recognize speech with your own Whisper model using Windows ML](./speech-recognition.md)
- [Run a model with the Windows ML Runtime API](./tutorial.md)
