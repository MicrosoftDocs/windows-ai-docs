---
title: Build and benchmark a model with Windows ML CLI
description: Build a ResNet-50 ONNX model with Windows ML CLI and measure a CPU baseline before optimizing for a GPU or NPU execution provider.
author: GrantMeStrength
ms.author: jken
ms.topic: quickstart
ms.date: 10/29/2026
---

# Quickstart: Build and benchmark a model

Use this quickstart to prepare ResNet-50, a pretrained model for classifying images, and measure how quickly it runs on your CPU. You'll export it to ONNX, a framework-independent model format, and keep 32-bit floating-point (FP32) values to establish an initial performance measurement before trying lower precision or other hardware. This benchmark uses generated inputs; it doesn't classify an image you supply or measure classification accuracy.

Install [Windows ML CLI](installation.md) first. For background on model formats and preparation steps, see the overview's [Key concepts](../../winml-cli.md#key-concepts). You don't need a neural processing unit (NPU) for this example; NPU workflows require Windows 11, version 24H2 or later and a compatible device, driver, and execution provider (EP), the runtime component that runs model operations on the target hardware.

## Verify the install

Run the following command to enumerate available devices and execution providers
on your machine:

```powershell
uv run winml sys --list-device --list-ep
```

`--list-device` and `--list-ep` print the hardware and EP inventory. Confirm that the
expected target appears; this check doesn't validate model loading or inference.
See [`winml sys`](../commands/sys.md) for the full flag reference.

## Inspect the model

Before downloading any models, confirm that Windows ML CLI recognises the model:

```powershell
uv run winml inspect -m microsoft/resnet-50
```

Review the detected image-classification task and loading/export configuration.
A recognized model can still encounter provider-specific limitations later.

> [!TIP]
> Always inspect before build to catch unsupported architectures early.

## Build the model

```powershell
uv run winml build -m microsoft/resnet-50 -o .\resnet_out --device cpu --ep cpu --precision fp32 --no-quant --no-compile
```

`winml build` generates a configuration and runs the enabled export, analysis, and optimization stages. This example explicitly skips quantization and compilation. See [`winml config`](../commands/config.md) to customize a workflow before running it.
The output directory preserves intermediate artifacts so you can inspect or reuse a stage.

After a successful build, you will find the following outputs in `resnet_out/`:

- **A standard ONNX file for each completed stage** — load, inspect, or pass any of these to a downstream tool independently.
- **`analyze_result.json`** — compatibility findings for the analysis target, including supported, partially supported, unsupported, and unknown operators and detected optimization patterns.
- **`winml_build_config.json`** — the resolved recipe for the enabled preparation stages.

Preserve external weight files referenced by the final `model.onnx`. To repeat
the build into the same directory intentionally, add `--rebuild`.

## Benchmark the model

```powershell
uv run winml perf -m .\resnet_out\model.onnx --device cpu --ep cpu --iterations 50 --monitor -o .\resnet_out\perf_cpu.json
```

The benchmark excludes warm-up iterations from measured latency and writes a JSON report. It uses generated inputs by default, so it doesn't establish task accuracy or end-to-end application performance.

To compare hardware, choose a compatible EP explicitly and keep input shapes, precision, and measurement settings consistent. `--device auto` prioritizes NPU, then GPU, then CPU; this is a selection policy, not a guarantee that the selected device is fastest for the model.

## What's next

- **[How Windows ML CLI Works](../concepts/how-it-works.md)** — understand what each command does under the hood.
- **[BERT sample](../samples/bert-config-build.md)** — see the config + build + perf workflow in detail with a representative model.
- **[Evaluate accuracy](../commands/eval.md)** — check output similarity and task metrics before deployment.
- **[Hugging Face model to NPU](../tutorials/prepare-image-classification-model.md)** — prepare a model for a specific hardware provider.

## See also

- [`winml build`](../commands/build.md)
- [`winml inspect`](../commands/inspect.md)
- [`winml perf`](../commands/perf.md)
- [`winml sys`](../commands/sys.md)
