---
title: Troubleshoot Windows ML CLI workflows
description: Resolve Windows ML CLI configuration, compilation, analysis, output, and environment issues without losing model artifacts or baselines.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Troubleshooting

Start with `winml sys` to identify package versions, providers, and devices. Add `-v` or `-vv` to the failing command for details. Keep the original model and use separate output paths while investigating.

## Compile

### Cannot enable compilation: no compile section

`build -c ... --compile` can't create a missing compile section. Generate a recipe that explicitly enables compilation for a compatible provider:

```powershell
winml config -m microsoft/resnet-50 --device npu --ep qnn --precision w8a16 --compile -o .\qnn_config.json
winml build -c .\qnn_config.json -m microsoft/resnet-50 -o .\qnn_output
```

This example requires a compatible Qualcomm NPU and provider on Windows 11, version 24H2 or later. `config -o` must name a JSON file, not a directory.

### Already a compiled EPContext model

An EPContext model isn't an input for recompilation. Return to the original or quantized ONNX graph and compile to a new path:

```powershell
winml compile -m .\model_quantized.onnx --device npu --ep qnn -o .\model_ctx.onnx
```

Use `--output-dir` instead of `-o` when you want the compiler to choose names in a directory.

### Provider does not support EPContext compilation

Not every EP supports offline compilation. Check available targets with `winml compile --list`. For CPU or DirectML workflows, skip that stage and benchmark the ONNX model directly:

```powershell
winml perf -m .\model.onnx --device cpu --ep cpu
```

Don't replace only the device name in an NPU recipe and expect its quantization and compilation settings to work unchanged.

## Analyze and optimize

### Unsupported or unknown nodes

Analyze the intended target and save optimization recommendations:

```powershell
winml analyze -m .\model.onnx --ep qnn --device npu --optim-config .\optim.json
winml optimize -m .\model.onnx -c .\optim.json --ep qnn --device npu -o .\model_optimized.onnx
winml analyze -m .\model_optimized.onnx --ep qnn --device npu
```

Unknown findings can reflect missing rules, shapes, or unsupported patterns. Optimization can help, but doesn't guarantee that unknowns disappear. Use `--run-unknown-op` only when the relevant local provider and hardware are available.

If a finding persists, review the reported dtype, shape, opset, and provider constraints. Try a supported precision or graph representation, or select a different target. When re-exporting, set `opset_version` through an `--export-config` JSON file; increasing its value isn't automatically a compatibility fix.

### Rule data unavailable

Static analysis requires the CLI's runtime rule data. Check that the installed release includes its required assets. For a source checkout, follow the repository's contributor setup rather than treating an absent rule database as a model failure.

## Build and output files

### Output already exists

Most file-producing commands protect existing outputs. Choose a new path to preserve a comparison baseline, or pass `--overwrite` when replacement is intentional. `build` uses `--rebuild` instead.

### Disk full

Exported graphs, external weights, caches, and compiled contexts can consume substantial space. Inspect the specific output and cache directories before removing files. Stop active builds, preserve final artifacts and referenced sidecars, and remove only files you no longer need.

The default cache is under `%USERPROFILE%\.cache\winml`. Don't delete the entire cache as a routine first step: it can contain other models, reports, and reusable artifacts.

### Final model fails after copying

Copy all external weight and EP context files referenced by the graph, preserving their relative paths. A GenAI bundle also needs its configuration and tokenizer assets. See [Output layout](reference/output-layout.md).

## Environment and model loading

Use the same Python 3.11 x64 environment for installation and execution. `uv run winml ...` uses the current project environment; a bare `winml` can resolve to a different installation.

If imports fail, inspect `winml sys` and the dependency resolver's output. Re-create or repair the isolated environment using the supported installation procedure rather than repeatedly changing shared packages. Model downloads can also require network access, authentication, or acceptance of the model's license.

Enable remote model or dataset code only when you trust its source.

## See also

- [Installation](getting-started/installation.md)
- [Sys command](commands/sys.md)
- [Analyze command](commands/analyze.md)
- [Execution providers and devices](concepts/execution-providers-devices.md)
