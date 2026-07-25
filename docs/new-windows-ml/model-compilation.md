---
title: Compile and cache ONNX models in Windows ML
description: Learn why Windows ML recommends pre-compiling ONNX models per execution provider and how compiled artifacts are cached per device.
author: Juan-
ms.author: jusepulv
ms.topic: how-to
ms.date: 07/22/2026
---

# Compile and cache ONNX models in Windows ML

Compilation is the step that turns your ONNX model into the hardware-specific binary an execution provider (EP) actually runs. On NPUs and GPUs that step can take anywhere from a few seconds to several minutes for large models — and if your app doesn't cache the result, users pay that cost every time your app starts.

This page explains what compilation involves, when to pre-compile, and how Windows ML caches the compiled artifact for you.

## Two kinds of compilation

"Compilation" can mean two different things. Separating them makes the rest of this page easier to reason about:

- **Graph optimization** — Fusion, constant folding, and layout work that ONNX Runtime performs at session creation. It's cheap, EP-agnostic, and runs every time you create a session.
- **EP compilation** — Conversion of the ONNX graph into an EP's own format, followed by compilation down to a hardware-specific binary. Hardware EPs (QNN, OpenVINO, VitisAI, NvTensorRtRtx, MIGraphX) do this, and it's the expensive step. On NPUs it can take tens of seconds to minutes for larger models.

Windows ML avoids re-paying EP compilation by using ONNX Runtime's [EPContext](https://onnxruntime.ai/docs/execution-providers/EP-Context-Design.html) mechanism. The first compile serializes a hardware-ready binary into a `*_ctx.onnx` model (or a sidecar `.bin`); later sessions load the pre-built binary and skip conversion entirely.

## Why pre-compile?

**Windows ML highly recommends pre-compilation when using execution providers.** Pre-compiling turns a multi-second — sometimes multi-minute — cold start into a one-time cost, and gives your app:

- **Fast cold starts** — subsequent launches skip graph conversion and hardware compilation.
- **Predictable behavior** — a driver, SDK, or EP version mismatch surfaces as an explicit `INVALID_GRAPH` error you can handle instead of silent slowdowns.
- **Lower CPU and battery use** — you stop recompiling the same graph on every launch.

Without pre-compilation, your app re-runs EP conversion and compilation on every session creation, and users feel the delay each time.

## When to pre-compile

Pre-compile whenever your app targets a hardware EP. Two timing options are available:

| Strategy | Best for | Trade-offs |
| --- | --- | --- |
| **Ahead-of-time (AOT)** *(recommended for most apps)* — compile at build or install time and ship the compiled artifact. | Enterprise deployments, apps targeting a fixed device profile, per-device installers. | Requires cross-compilation tooling for targets your build machine can't run. |
| **Compile on first run** (simplest)  — check for a compiled artifact, compile it if missing, then cache and reuse it. | Store apps and general consumer distribution across a diverse hardware base. | Users pay the compile cost once, on first launch. |

Compile-on-first-run is the pattern shown in the [Windows ML walkthrough](tutorial.md#ep-compilation). It uses `OrtModelCompilationOptions.CompileModel()` (available in the ONNX Runtime shipped with Windows ML, ORT 1.22 and later) to produce a compiled artifact next to your model, and reuses it on every subsequent run. For the API in context, see [Compile models](#compile-models).

## Compiled models are device-specific
A compiled model is bound to the specific EP it was compiled for.

## Compiled models may-require re-compiling
EP updates and driver updates can cause a previously-compiled model to no longer be valid. We recommend that when the EP or driver version changes, developers test the previously-compiled model (by loading an inference session) and validate the output, otherwise re-compile.
Plan for cache invalidation: catch `INVALID_GRAPH` errors from the session and recompile to refresh the cached artifact. Common triggers include:

- A new EP is installed.
- The GPU or NPU driver is updated.
- The user's hardware changes.

If your app is distributed across a range of devices, store compiled artifacts in a local cache location so each device produces and reuses its own binary.

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
- [Windows ML execution providers](supported-execution-providers.md) — versions of the EPs your app can target
- [EP Context Design](https://onnxruntime.ai/docs/execution-providers/EP-Context-Design.html) *(for EP authors)* — the ONNX Runtime design behind the `*_ctx.onnx` artifact
