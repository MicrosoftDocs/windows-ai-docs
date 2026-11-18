---
title: Windows ML Runtime API concepts
description: Learn the core concepts behind the experimental Windows ML Runtime API, including runtime, model, execution target, pipeline, stage, tensor, and tokenizer interfaces.
ms.date: 11/18/2026
ms.topic: concept-article
---

# Windows ML Runtime API concepts

> [!IMPORTANT]
> The Windows ML Runtime API is currently experimental and **not supported** for use in production environments. Apps trying out this API should not be published to the Microsoft Store.

The Windows ML Runtime API is a native local AI inferencing framework built specifically for Windows. With the Windows ML Runtime API, you have explicit control over model loading, hardware placement, tensor ownership, pipeline composition, compilation, and diagnostics.

We recommend using the Runtime API for the best model inferencing performance on Windows, but if your app needs to run on other platforms, or if you already have working cross-platform ONNX Runtime code, you can also [use ONNX Runtime via Windows ML](../get-started.md).

## How the objects fit together

```text
IWinMLRuntime
  |-- loads --> IWinMLModel
  |-- creates -> IWinMLExecutionTarget
  `-- creates -> IWinMLPipelineBuilder
                   |-- adds/connects -> IWinMLStage
                   `-- Build() ------> IWinMLPipeline

IWinMLExecutionTarget -- creates --> IWinMLTensor
IWinMLStage ----------- binds -----> IWinMLTensor
IWinMLPipeline -------- Run() -----> stage outputs
```

Every method returns `HRESULT`, and every interface pointer follows normal COM
reference counting. Optional capabilities are discovered with `QueryInterface`;
an unavailable capability returns `E_NOINTERFACE`.

## Runtime

Create `IWinMLRuntime` with `WinMLCreateRuntime`. Use it to load model
artifacts, create execution targets, and create pipeline builders.

## Models and schema

`IWinMLModel` is an immutable, opaque model artifact. `LoadModelFromFile`
accepts any absolute or relative file path your app can read; model location is
your app's packaging decision.

Loading verifies that the artifact is recognized and structurally readable.
Target compatibility is committed later, when the pipeline builder's `Build`
method succeeds.

Reflect a model's declared ordinal schema through `IWinMLModelSchema`. Some
ONNX artifacts also expose names through the optional `IWinMLOrtModelSchema`,
but execution identity remains positional.

## Shapes and operators

A declared model schema can contain free dimensions, represented by
`UINT64_MAX`. A concrete `IWinMLTensor` always has resolved dimensions.
ORT-backed pipelines can bind and run concrete values for a free dimension.

Fixed or explicitly bounded shapes remain the optimized design center because
they let `Build` validate compatibility, allocate stable resources, and
determine whether replay or device-resident iteration is legal. They also
provide the widest compatibility across execution backends. This is an
optimization and portability distinction, not a blanket rejection of models
whose declared ONNX schema contains free dimensions.

Standard ONNX operators likewise improve backend portability. A successful
model load does not guarantee that every target can execute every operator;
`Build` is the compatibility check.

## Backends

In a pipeline that you build, the model file selects the backend. Your app uses the same Runtime objects
either way: model, execution target, pipeline builder, stages, bindings, and
`Run`.

> [!NOTE]
> This table covers models that you add to a pipeline with `AddModelStage`. A Runtime pipeline doesn't take GGUF or SafeTensors models. Those models run only through the Task APIs, where you pass the loaded model to a Task with `SetModel`. See [Choose how a Task runs your model](../tasks/choose-model-path.md).

| Model file | Backend in a pipeline | Hardware acceleration |
|---|---|---|
| `.onnx`, `.ort` | ONNX Runtime | Execution providers (EPs) that Windows installs and keeps up to date through the Windows ML EP catalog |
| `.oir`, `.mlirbc`, `.mlir` | CGC | Models compiled for a Direct3D 12 GPU target. See [The CGC backend](./model-compilation-execution.md). |

An ONNX Runtime stage can express any ONNX graph, and you can connect several stages in one pipeline. Declare sequence state (a KV cache) with state tensor pairs through `IWinMLStatefulStageOptions`, and set the context length with `SetSequenceCapacityHint` before `Build`. After `Build`, `IWinMLOrtStageDiagnostics` names the requested and selected providers.

One pipeline can mix backends. For example, a Whisper stage on ONNX Runtime can
feed a stage that runs a CGC artifact.

## Execution targets

`IWinMLExecutionTarget` is an immutable placement token that requests a
hardware class, and optionally a specific adapter. Each backend resolves it
when `Build` runs:

| Stage target | ONNX Runtime |
|---|---|
| `nullptr` passed to `AddModelStage` | Automatic placement selects a compatible GPU EP when available, with CPU fallback. |
| `CreateCpuExecutionTarget` | Selects a compatible CPU EP, with ORT's CPU provider as the baseline. |
| `CreateExecutionTarget` with the GPU kind, `CreateExecutionTargetFromAdapter`, or `CreateExecutionTargetFromD3D12` | Maps the adapter identity to a compatible GPU EP device. If no provider device matches, `Build` fails. |
| `CreateExecutionTarget` with the NPU kind, or an NPU adapter | Maps to a compatible NPU EP device. |
| `IWinMLOrtCompatibility::CreateExecutionTarget(provider, kind, hardwareTarget)` | Pins one registered EP. With `hardwareTarget` set to `nullptr`, the Runtime pairs the provider with one of its own devices; on a PC with two GPUs, this avoids pairing the provider with an adapter it doesn't support. |

`IWinMLRuntime::CreateExecutionTarget(kind, preference)` chooses an adapter of
the requested class without requiring you to enumerate devices yourself, and
`preference` ranks adapters when there's more than one, for example an
integrated and a discrete GPU:

```cpp
ComPtr<IWinMLExecutionTarget> target;
THROW_IF_FAILED(runtime->CreateExecutionTarget(
    WINML_EXECUTION_TARGET_KIND_GPU,
    WINML_EXECUTION_TARGET_PREFERENCE_PERFORMANCE,
    target.GetAddressOf()));
```

Explicit adapter selection requires a compatible ONNX Runtime provider to
expose that hardware identity through ORT. If no provider device matches,
`Build` fails rather than silently selecting another adapter or CPU.

`CreateExecutionTargetFromD3D12` retains the caller's device and optional queue
on the target, but ORT execution uses only the adapter identity for EP
selection. The ORT provider owns its execution resources, and the caller's
command queue isn't used for ORT submission.

A D3D12 target still exposes `IWinMLD3D12ExecutionTarget` through
`QueryInterface`. Reusing one explicit target across stages expresses
same-adapter affinity; distinct targets make transfer boundaries explicit.

## Pipelines and stages

Build a pipeline with the one-shot `IWinMLPipelineBuilder`:

1. Add model or processor stages with their execution targets.
2. Connect stage ports by ordinal index.
3. Call `Build` once to freeze the graph, resolve placement, and validate
   compatibility.

For two model stages, add both stages, connect the first stage's output ordinal
to the second stage's input ordinal, and build once. Your app supplies
the models and binds any external inputs. It doesn't need a model-specific
Runtime API: embedding, decoder, image preprocessing, and other application
architectures use the same stage and tensor primitives.

Each `IWinMLStage` owns positional input bindings and exposes its current
output tensors. Input bindings persist across runs. Call `BindOutput` before an
execution when your app supplies output storage.

Optional stage capabilities include resolved target, materialized schema,
output-residency policy, and sequence-state control. Use `QueryInterface` to
test for each capability.

## Pipeline execution

`IWinMLPipeline::Run` executes the prepared pipeline synchronously. Call
`ResetExecutionState` between independent sequences to clear runtime-managed
state.

Optional execution interfaces provide device-timeline submission and
fixed-count iterative replay when the built graph and selected backend support
them. The basic `Run` path remains the common baseline.

## Tensors and locks

`IWinMLTensor` represents typed, multi-dimensional data. Create tensors from a
factory queried from the execution target so tensor placement follows target
placement.

Factories cover raw bytes, image data, audio data, token IDs, and composable
tensor processors. Optional tensor interfaces expose mutation, D3D12 buffer
bindings, and synchronization.

Call `IWinMLTensor::Lock` before accessing tensor bytes on the CPU. The
returned `IWinMLTensorDataLock` establishes the CPU-access window, performs
required mapping or synchronization, and owns the lifetime of the pointer
returned by `GetData`.

## Compilation and external weights

Ahead-of-time compilation is an optional capability of an execution target,
exposed through `IWinMLModelCompiler`. Compiled artifacts reload through the
normal model-loading methods. Apps can supply externalized weights through
`IWinMLResourceMapReader`. On a GPU target that isn't pinned to an execution
provider, the compiler writes a device-targeted or durable artifact for the
[CGC backend](./model-compilation-execution.md#compile-a-model-for-cgc).

## Text workloads

Text workloads use the same runtime, target, pipeline, stage, and tensor
primitives. Your app can own model-package metadata, sampling, stopping
policy, and generated-token accumulation directly, or use the [Text
Generation API](./text-generation.md) to coordinate those operations over a compatible Runtime
pipeline. In Windows ML 2.9, a Task can also [take a model](../tasks/choose-model-path.md) instead of a pipeline.

`IWinMLTokenizer` provides low-level encode and decode operations. An
`IWinMLTokenizerDecoder` maintains an independent incremental-decode cursor for
one sequence. The Text Generation API adds text-generation sessions, options, streaming
updates, cancellation, finish reasons, and completed results without changing
ownership of the underlying Runtime objects.

## ONNX Runtime compatibility

The optional interfaces in `WinMLRuntimeOrt.h` provide ONNX conveniences:

- `IWinMLOrtCompatibility` selects an already-registered ONNX Runtime
  execution provider.
- `IWinMLOrtModelSchema` reflects input and output names.
- `IWinMLOrtNamedBindings` binds an ORT-backed stage by name.
- `IWinMLOrtStageDiagnostics` reports requested and selected providers after
  `Build`.

These interfaces resolve names and provider policy during setup. Execution
continues to use the same positional pipeline contract.

Query `IWinMLOrtCompatibility` from the runtime to pin an ONNX model stage to
an execution provider you've already registered, instead of letting `Build`
choose one automatically:

```cpp
ComPtr<IWinMLOrtCompatibility> ortCompatibility;
THROW_IF_FAILED(runtime->QueryInterface(IID_PPV_ARGS(ortCompatibility.GetAddressOf())));

ComPtr<IWinMLExecutionTarget> target;
THROW_IF_FAILED(ortCompatibility->CreateExecutionTarget(
    L"MyRegisteredProvider",
    WINML_EXECUTION_TARGET_KIND_GPU,
    /* hardwareTarget */ nullptr,
    target.GetAddressOf()));
```

Passing `nullptr` for `hardwareTarget` lets the Runtime pair the provider with
one of its own devices of that hardware class; pass an existing
`IWinMLExecutionTarget` to pin the provider to that specific adapter instead.

In Windows ML 2.9, a generic GPU target no longer downloads the WebGPU execution
provider. It uses WebGPU only when its package is already installed, so on a PC
with neither WebGPU nor another GPU provider, `Build` fails with
`ERROR_NOT_SUPPORTED`. To use WebGPU, pin it with
`IWinMLOrtCompatibility::CreateExecutionTarget`; the Runtime acquires and
registers the provider when the pipeline builds. WebGPU runs models but can't
compile them. See [WebGPU execution provider](../webgpu-ep.md).

## See also

- [Set up a project for the Windows ML Runtime API](./get-started.md)
- [The CGC backend in Windows ML](./model-compilation-execution.md)
- [Windows ML Task APIs overview](../tasks/overview.md)
- [Generate text with your own language model using Windows ML](./text-generation.md)
- [Recognize speech with your own model using Windows ML](./speech-recognition.md)
- [Run a model with the Windows ML Runtime API](./tutorial.md)
