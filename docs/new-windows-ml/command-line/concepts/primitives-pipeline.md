---
title: Choose individual commands or a Windows ML CLI build
description: Choose individual Windows ML CLI stages for targeted experiments or a build configuration for reusable model preparation workflows.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Primitives and pipeline

Use individual commands when you need to inspect or vary one transformation. Use `winml build` when you want the CLI to coordinate a reusable model-preparation recipe.

## The primitive commands

| Command | Input | Result |
|---|---|---|
| `export` | Supported source model | ONNX graph and associated metadata or external data. |
| `analyze` | ONNX graph | Compatibility findings and optional optimizer recommendations. |
| `optimize` | ONNX graph | Transformed graph. |
| `quantize` | ONNX graph | FP16, weight-only, dynamic, or static-quantized candidate. |
| `compile` | Graph prepared for a compatible EP | Target-specific compiled model and any context files. |
| `perf` | Runnable model or supported source workflow | Timing and optional resource report. |
| `eval` | Candidate model and dataset or reference | Task metrics or output-similarity report. |

Not every command produces another ONNX file. Analysis and validation reports help you decide whether to continue; they aren't model stages to pass as `-m`.

You can enter at the stage appropriate to your input. An existing ONNX graph doesn't need export, and a precompiled EPContext model shouldn't be compiled again. Preserve baselines and name outputs explicitly.

## The pipeline wrapper

`winml build` coordinates enabled export, optimization, quantization, and compilation stages, including the analyzer/optimizer feedback loop:

```powershell
winml config -m microsoft/resnet-50 --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\config.json
winml build -c .\config.json -m microsoft/resnet-50 -o .\output
```

For an existing ONNX input, export is skipped. Set `quant` or `compile` to `null` in the recipe to skip those stages, or use the corresponding negative command-line options.

An automatically generated recipe excludes compilation unless you request it. With an existing recipe, enabling compilation requires a non-null compile section. See [Config and build](configuration-build.md).

After building, run `perf` and `eval` separately. A successful build doesn't establish accuracy or performance.

## When to choose which

Choose individual commands to:

- Learn how a transformation changes the graph.
- Investigate a failing stage without rerunning the whole preparation workflow.
- Compare precision or optimization candidates against a preserved baseline.
- Integrate a stage into a script that already manages its artifacts.

Choose `build` to:

- Review and share model-preparation settings as JSON.
- Coordinate the analyzer feedback loop.
- Reuse completed artifacts through the cache.
- Run a consistent recipe in automation.

The paths can produce different artifacts if their defaults or settings differ. To make a meaningful comparison, align the model revision, target, precision, calibration inputs, and optimization settings.

## See also

- [How Windows ML CLI works](how-it-works.md)
- [Build command](../commands/build.md)
- [Config and build](configuration-build.md)
- [Hugging Face model to NPU](../tutorials/prepare-image-classification-model.md)
