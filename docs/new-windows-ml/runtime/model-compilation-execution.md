---
title: The CGC backend in Windows ML
description: Learn how the experimental CGC backend in Windows ML 2.9 compiles models for your GPU's silicon and runs them on the Direct3D 12 device you pick.
author: andrewleader
ms.author: aleader
ms.topic: concept-article
ms.date: 11/18/2026
---

# The CGC backend in Windows ML

> [!IMPORTANT]
> The CGC backend is experimental, **not supported** for use in production environments, and new in Windows ML 2.9 experimental.

CGC is the Windows-native backend that compiles a model into programs for a Direct3D 12 device and runs them on the GPU target your app picked. It runs on the GPU only, with no CPU fallback. Finding a GPU doesn't establish that CGC supports its driver or a given model: a model that CGC can't run fails to load with an error.

## Benefits

- **Hardware-specific acceleration, with shared fallbacks built in.** CGC compiles your model for the target silicon by using compiler backends from AMD, Intel, NVIDIA, and Qualcomm. Shared DirectX fallbacks keep models running and make the experience more consistent across hardware. Deep DirectX integration adds OS-level scheduling, resource management, and debuggability and profiling with PIX.
- **Bring your own model, convert once, run anywhere.** Convert your model once into a single representation, the WinML Unified IR, that runs well across supported hardware. Prepare it ahead of time (AOT) with the Windows ML CLI, or on the device, either AOT or just in time (JIT), without maintaining a separate model for each vendor. For common scenarios like text generation and speech recognition, the higher-level [Task APIs](../tasks/overview.md) build on the Runtime APIs and CGC, so you can run your model in just a few lines of code.

## Recommended models

CGC is experimental. We have validated the following models thoroughly. Other models might load, but they're likely to fail with `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`, run slowly, or give different results.

| Kind | Models |
|---|---|
| Non-LLM | YOLO11 (`yolo11n`, `yolo11s`, `yolo11m`, `yolo11l`, `yolo11x`), YOLOv8 (`yolov8n`, `yolov8s`, `yolov8m`, `yolov8l`, `yolov8x`), CLIP text, CLIP vision, ResNet-50, `whisper-encoder`, `whisper-decoder`, `whisper-decoder-with-past` |
| LLM (GGUF or SafeTensors) | Llama-3.2-1B-Instruct, Qwen 3.0 0.6B, Gemma 3 1B It, Phi-3.5-mini-instruct, Phi4-14B-instruct, Qwen2.5-0.5B-Instruct, Qwen3.5-0.8B, Qwen3.5-9B, and Qwen3.8-27B (NVIDIA GPUs only) |
| Speech recognition (SafeTensors or ONNX) | Whisper Medium, Whisper tiny, Nemotron 3.5 ASR Streaming 0.6B |
| Decision (SafeTensors or ONNX) | Laya |

The models that the Windows ML samples use are part of this list, so the samples run on supported models. The list includes Qwen2.5-0.5B-Instruct, Whisper tiny, and Laya from the Task samples, and the Qwen3.5 models from the Task Studio app.

## Recommended hardware

CGC is currently experimental, and only supports GPUs right now. We have validated CGC across various Windows IHVs, but for the best performance and support, we recommend testing against NVIDIA GPUs with the latest drivers during the initial experimental release.

## Ways to use the CGC backend

| Input format | How to use |
|---|---|
| Published weights that you specify for a Task | `SetModel` or `CreateDecisionTaskFromModel` with a model loaded by `LoadModelFromPath` or `LoadModelFromFile`. Windows ML reads the model, and the first session compiles its programs. |
| A CGC artifact in a pipeline or a composition | `LoadModelFromFile` or `LoadModelFromBuffer` loads an `.oir`, `.mlirbc`, or `.mlir` artifact, and `AddModelStage` or `SetComposition` runs it as a CGC stage. |
| An ONNX model that you run with ONNX Runtime | Use the Windows ML CG EP, `WinMLCGExecutionProvider`, with the ONNX Runtime API. See [Windows ML CG EP (experimental)](../supported-execution-providers.md#windows-ml-cg-ep-experimental). |

### Runtime APIs with CGC or the CG EP

We recommend the Runtime APIs (and the Task APIs built on them) with CGC over the CG EP. The Runtime APIs give CGC the whole model and control over placement, tensors, and compilation, so they're generally the more performant path over time.

In the short term, the CG EP can be faster for some workloads, particularly on the fallback path, because ONNX Runtime applies graph pre-optimizations before the CG EP receives the model. Those optimizations can help or hurt performance depending on the workload, so measure both paths on your model if performance matters. The CG EP is also the lower-friction way to try CGC when you already have ONNX Runtime code.

## Compile a model for CGC

Use `IWinMLModelCompiler` on a GPU target that isn't pinned to an execution provider. The compiler imports an ONNX model and writes one of two compiled forms. Compilation lowers automatically, so you don't query for lowering options.

| Form | File extension | Behavior |
|---|---|---|
| Device-targeted (default) | `.oir` | Finishes compilation for the device, driver, and Windows ML version that compiled it. It loads fastest, and another device or software version rejects it. |
| Durable | `.mlirbc` | Stops before the hardware-specific work and finishes it when the artifact loads, so it keeps loading across devices and updates. |

Name the artifact with the matching extension so that `LoadModelFromFile` recognizes it. This differs from [compiling an ONNX model for an execution provider](../model-compilation.md), which produces an EP context model.

## Cache compiled programs

The first session of a Task that uses the CGC backend compiles its programs, which can take several seconds. A program cache directory keeps them for later processes. For options and disk-space tradeoffs, see [Cache compiled programs](../tasks/choose-model-path.md#cache-compiled-programs).

## Diagnose a stage

`IWinMLStageExecutionDiagnostics` reports a CGC stage's work as compiled subgraphs, grouped by execution technology, such as `D3D12_DRIVER` and `DIRECTML`. For deeper debugging and profiling, use PIX.

## Deploy the CGC backend

- The `Microsoft.Windows.AI.MachineLearning` package depends on `Microsoft.Direct3D.D3D12` 1.620.1, which NuGet restores with it, and deploys `D3D12\D3D12Core.dll` beside your app, including in .NET publish output.
- The CGC modules and `DirectML.dll` deploy beside your app for x64 and Arm64. Packages with the CGC backend deploy DirectML even when `WinMLCopyDirectML` is `false`, because the backend requires it.
- The CG EP library, `WinMLCGEp.dll`, deploys beside your app with ONNX Runtime. See [Windows ML CG EP (experimental)](../supported-execution-providers.md#windows-ml-cg-ep-experimental).
- ARM64EC apps get no CGC payload.
- The CGC and direct-load Task modules make the native payload about 110 MB per architecture, a little more than twice the size in Windows ML 2.7.

## See also

- [Windows ML Runtime API concepts](./concepts.md)
- [Windows ML Task APIs overview](../tasks/overview.md)
