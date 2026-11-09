---
title: Prepare a Hugging Face model for an NPU
description: Export ConvNeXt, inspect QNN compatibility, quantize with representative data, and compile and validate the model using Windows ML CLI.
author: GrantMeStrength
ms.author: jken
ms.topic: tutorial
ms.date: 11/03/2026
---

# Hugging Face model to NPU

This tutorial prepares `facebook/convnext-tiny-224` for a Qualcomm NPU with the QNN execution provider. You first run individual stages so you can inspect their artifacts, then learn how to reuse a build configuration.

For a CPU introduction without NPU prerequisites, use the [quickstart](../getting-started/quickstart.md).

## Prerequisites

- Windows 11, version 24H2 or later.
- A compatible Qualcomm NPU, its driver, and the QNN provider.
- [Windows ML CLI installed](../getting-started/installation.md) in a Python 3.11 x64 environment.
- Network access for model weights and calibration/evaluation data, including any required dataset access approval.

Run commands from the environment directory. Confirm the target with `uv run winml sys --list-ep` and `uv run winml sys --list-device`. Other NPUs require a recipe validated for their provider; changing only `--ep` doesn't establish compatibility.

## 1. Inspect and export

```powershell
uv run winml inspect -m facebook/convnext-tiny-224 --task image-classification
uv run winml export -m facebook/convnext-tiny-224 --task image-classification -o .\convnext.onnx
```

Inspection identifies supported loading and export workflows. It doesn't guarantee that later quantization or compilation succeeds. Keep the exported graph and any external weights as your baseline.

## 2. Analyze and optimize for QNN

```powershell
uv run winml analyze -m .\convnext.onnx --ep qnn --device npu --optim-config .\optim.json
uv run winml optimize -m .\convnext.onnx -c .\optim.json --ep qnn --device npu -o .\convnext_optimized.onnx
uv run winml analyze -m .\convnext_optimized.onnx --ep qnn --device npu
```

Review unsupported, partial, and unknown findings before proceeding. The floating-point graph may need quantization or provider-specific transformations. Static support findings aren't proof of complete model execution.

## 3. Quantize with task-aware calibration

```powershell
uv run winml quantize -m .\convnext_optimized.onnx --precision w8a16 --task image-classification --model-id facebook/convnext-tiny-224 --samples 32 -o .\convnext_quantized.onnx
```

This uses the task's default calibration dataset and matching processor. To select a supported alternative, set `quant.dataset_name` in a build configuration and run it with `winml build -c`, as shown in [Reuse the workflow with config and build](#reuse-the-workflow-with-config-and-build). Standalone `winml quantize -c` doesn't apply `quant.dataset_name`. The sample count is an initial development setting, not an accuracy recommendation.

Verify that calibration uses representative images. Random inputs are useful for checking a workflow but aren't sufficient for deployment calibration. See [Quantization](../concepts/quantization.md).

## 4. Evaluate the quantized candidate

```powershell
uv run winml eval -m .\convnext.onnx --model-id facebook/convnext-tiny-224 --task image-classification --device cpu --ep cpu --samples 100 --no-shuffle -o .\baseline_eval.json
uv run winml eval -m .\convnext_quantized.onnx --model-id facebook/convnext-tiny-224 --task image-classification --device npu --ep qnn --samples 100 --no-shuffle -o .\quantized_eval.json
```

Use the same dataset revision, split, preprocessing, and sample selection for both runs. Review the accuracy difference against your application's requirements. If the quality change is unacceptable, revisit calibration or precision before compiling.

## 5. Compile and benchmark

```powershell
uv run winml compile -m .\convnext_quantized.onnx --device npu --ep qnn -o .\convnext_ctx.onnx
uv run winml perf -m .\convnext_ctx.onnx --device npu --ep qnn --warmup 10 --iterations 50 --monitor -o .\convnext_perf.json
```

Keep the generated context sidecars with the ONNX model. Benchmark results depend on drivers, device state, input shapes, and measurement settings; the tutorial doesn't prescribe an expected latency.

Evaluate the final compiled artifact as well before deployment. A successful compile or benchmark doesn't establish accuracy.

## Reuse the workflow with config and build

Generate an explicit target recipe, review its calibration and optimization sections, then run the build:

```powershell
uv run winml config -m facebook/convnext-tiny-224 --task image-classification --device npu --ep qnn --precision w8a16 --compile -o .\convnext_config.json
uv run winml build -c .\convnext_config.json -m facebook/convnext-tiny-224 -o .\convnext_build
```

Before running `build`, edit the generated configuration's `quant` section if you need a supported alternative `dataset_name`, the same `samples` count as the manual workflow, or other calibration settings. `build` consumes the full quantization configuration and coordinates enabled preparation stages and the analyzer feedback loop. Run `perf` and `eval` on its reported final artifact separately.

Use `--overwrite` to replace an existing generated config and `--rebuild` to rerun an existing build intentionally.

## Use another target

For Intel NPU, select OpenVINO and a supported model/precision recipe. For CPU or DirectML, start from an uncompiled floating-point baseline instead of replaying QNN-specific quantization and compilation.

See [Execution providers and devices](../concepts/execution-providers-devices.md) and [Compile command](../commands/compile.md) for target selection.

## See also

- [Bring your own ONNX model](build-existing-model.md)
- [Config and build](../concepts/configuration-build.md)
- [Evaluate model quality](../concepts/evaluation-datasets.md)
- [Output layout](../reference/output-layout.md)
