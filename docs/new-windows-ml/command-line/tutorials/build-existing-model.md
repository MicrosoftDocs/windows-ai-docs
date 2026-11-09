---
title: Prepare an existing ONNX model with Windows ML CLI
description: Analyze and optimize an existing ONNX graph, compare it with a baseline, and choose optional precision and compilation steps for Windows.
author: GrantMeStrength
ms.author: jken
ms.topic: tutorial
ms.date: 10/29/2026
---

# Bring your own ONNX model

Start from an existing ONNX graph, preserve a baseline, and validate each transformation. This tutorial uses CPU for the initial workflow so it doesn't assume that your model is already suitable for a particular NPU.

## Prerequisites

- [Windows ML CLI installed](../getting-started/installation.md) in a Python 3.11 x64 environment.
- An ONNX file and all its referenced external data files.
- Knowledge of the model's input names, shapes, dtypes, and preprocessing.
- For NPU steps, Windows 11, version 24H2 or later, compatible hardware, drivers, and an EP.

Replace `my_model.onnx` with your input path. The commands assume that the model can run on CPU and that generated inputs are valid for its shapes; supply representative input tensors when needed.

## 1. Analyze the baseline

```powershell
uv run winml analyze -m .\my_model.onnx --ep cpu --device cpu --output .\baseline_analysis.json --optim-config .\optim.json
```

Review the findings and recommended transformations. Analysis depends on available rules and doesn't prove full runtime compatibility.

## 2. Optimize and re-analyze

```powershell
uv run winml optimize -m .\my_model.onnx -c .\optim.json --ep cpu --device cpu -o .\my_model_optimized.onnx
uv run winml analyze -m .\my_model_optimized.onnx --ep cpu --device cpu --output .\optimized_analysis.json
```

The optimizer can change numerical behavior and provider compatibility. Fewer nodes don't necessarily mean lower latency. Keep both graphs for comparison.

## 3. Compare outputs and performance

```powershell
uv run winml eval -m .\my_model_optimized.onnx --mode compare --reference .\my_model.onnx --device cpu --ep cpu --samples 10 -o .\comparison.json
uv run winml perf -m .\my_model.onnx --device cpu --ep cpu --warmup 10 --iterations 50 -o .\baseline_perf.json
uv run winml perf -m .\my_model_optimized.onnx --device cpu --ep cpu --warmup 10 --iterations 50 -o .\optimized_perf.json
```

Compare mode measures output similarity, not task accuracy. Add representative tensors with `--input-data` where supported, and use labeled evaluation data to measure application quality. The archive conventions differ between compare mode and perf; see [Eval command](../commands/eval.md).

## 4. Choose optional precision conversion

Use a separate output for each candidate. For example, dynamic quantization doesn't require calibration:

```powershell
uv run winml quantize -m .\my_model_optimized.onnx --precision dynamic -o .\my_model_dynamic.onnx
```

Dynamic quantization is a candidate for supported CPU models, not a universal optimization. Other options include FP16 conversion, INT4 weight-only conversion, and calibrated static QDQ. Select a mode supported by your target, then repeat quality and performance validation.

For static calibration of a known Hugging Face-derived model, provide its task and model ID so the correct data and preprocessing can be selected. Generic random calibration is only a workflow check. See [Quantization](../concepts/quantization.md).

## 5. Compile only when the target supports it

CPU and DirectML don't require an offline EPContext step. For a compatible, appropriately prepared QNN model on a Qualcomm NPU, compilation takes this form:

```powershell
uv run winml compile -m .\my_model_qnn_ready.onnx --device npu --ep qnn -o .\my_model_ctx.onnx
```

`my_model_qnn_ready.onnx` represents a separately validated QNN candidate, not the dynamic CPU artifact from the previous step. Check available compiler targets with `winml compile --list`, retain referenced context files, and validate the final artifact on that hardware.

## Automate with config and build

Generate a recipe for the original ONNX model:

```powershell
uv run winml config -m .\my_model.onnx --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\onnx_config.json
uv run winml build -c .\onnx_config.json -m .\my_model.onnx -o .\onnx_build --no-quant --no-compile
```

The config uses `export: null` because the input is already ONNX. The build runs its enabled stages and saves the resolved configuration and final model. Run quality and performance checks separately.

For an already-quantized input, inspect the graph and use `--no-quant --no-optimize` when you need to preserve it rather than applying floating-point transformations.

## See also

- [Analyze and optimize](../concepts/analysis-optimization.md)
- [Hugging Face model to NPU](prepare-image-classification-model.md)
- [Config and build](../concepts/configuration-build.md)
- [Output layout](../reference/output-layout.md)
