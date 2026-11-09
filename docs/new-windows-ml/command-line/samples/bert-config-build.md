---
title: Build and benchmark BERT with Windows ML CLI
description: Generate a reusable BERT configuration, build an ONNX CPU baseline, and benchmark the model before choosing a hardware-specific recipe.
author: GrantMeStrength
ms.author: jken
ms.topic: tutorial
ms.date: 10/29/2026
---

# BERT: Configure, build, and benchmark

This sample uses BERT's pretrained masked-language-model task to demonstrate a reusable configuration workflow. It establishes an unquantized CPU baseline rather than adding an untrained classification head to a base checkpoint.

## Prerequisites

- Complete [installation](../getting-started/installation.md) in a Python 3.11 x64 environment.
- Provide network access to download `google-bert/bert-base-uncased`.
- For later NPU experiments, use Windows 11, version 24H2 or later with compatible hardware, drivers, and an EP.

Run the following commands from the environment directory.

## 1. Inspect and configure

```powershell
uv run winml inspect -m google-bert/bert-base-uncased --task fill-mask
uv run winml config -m google-bert/bert-base-uncased --task fill-mask --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\bert_cpu.json
```

Review the generated export inputs, shapes, optimization settings, and null quantization/compile sections. Use the generated configuration rather than copying an abbreviated schema excerpt into a build.

## 2. Build the ONNX model

```powershell
uv run winml build -c .\bert_cpu.json -m google-bert/bert-base-uncased -o .\bert_cpu --no-quant --no-compile
```

The build runs the enabled export, analysis, and optimization stages. It reports the final model path and saves the resolved configuration. Preserve external weight files referenced by the final ONNX model.

If you intentionally repeat the build into the same directory, add `--rebuild`.

## 3. Benchmark the baseline

```powershell
uv run winml perf -m .\bert_cpu\model.onnx --device cpu --ep cpu --warmup 10 --iterations 50 -o .\bert_cpu\perf.json
```

Review latency percentiles and throughput. The default generated inputs exercise the exported tensor shapes, not realistic token distributions or application preprocessing. Supply representative tensors when you need a workload-specific measurement.

## 4. Compare model quality

Compare the optimized graph with its exported baseline:

```powershell
uv run winml eval -m .\bert_cpu\model.onnx --mode compare --reference .\bert_cpu\export.onnx --device cpu --ep cpu --samples 10 -o .\bert_cpu\comparison.json
```

Output similarity helps detect numerical changes. It doesn't replace task-level evaluation on representative data; use the [evaluation guidance](../concepts/evaluation-datasets.md) to select appropriate metrics.

## Target other hardware

Generate a separate configuration and output directory for each target. Choose a supported precision, use representative calibration data when required, and pass `--compile` during configuration only when the EP supports offline compilation.

Don't reuse CPU baseline timings as expected NPU performance or assume the same graph transformations suit every provider.

## See also

- [Config command](../commands/config.md)
- [Build command](../commands/build.md)
- [Perf command](../commands/perf.md)
- [Hugging Face model to NPU](../tutorials/prepare-image-classification-model.md)
