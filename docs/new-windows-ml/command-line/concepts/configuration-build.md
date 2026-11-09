---
title: Configure repeatable builds with Windows ML CLI
description: Generate, review, and reuse Windows ML CLI build configurations, select pipeline stages, and record dependencies for repeatable builds.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Config and build

`winml config` generates a JSON recipe for a supported model and target. `winml build` consumes that recipe to run the enabled model-preparation stages. Keeping these steps separate lets you review settings and share them with your team before a longer build.

## Generating a config

Start with an explicit baseline target:

```powershell
winml config -m microsoft/resnet-50 --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\resnet50.json
```

Review the generated file. Specify `--task` when the task is ambiguous. Use `--compile` only for a target that supports offline compilation, and use a separate configuration for each hardware/precision combination.

Without explicit precision, automatic resolution is target-dependent: NPU selects `w8a16`, while CPU/GPU selects `fp16`. Model-specific recipes can further affect the generated configuration. A build precision selector isn't equivalent to converting every existing ONNX tensor; use [standalone quantization](../commands/quantize.md) when you need an explicit conversion.

`config` writes JSON to stdout when you omit `-o`. If you use an output file, existing files are protected unless you pass `--overwrite`.

## What's in a config

The main sections are `loader`, `export`, `optim`, `quant`, and `compile`. An already-exported ONNX input uses `export: null`; `quant: null` and `compile: null` disable those stages.

The configuration can also include evaluation settings and workflow controls. Run `winml eval` and `winml perf` explicitly to validate the built artifact rather than assuming a successful build measures accuracy or performance.

See the [build configuration reference](../reference/index.md) for field meanings and valid examples.

## Consuming a config

Supply the source model along with the recipe:

```powershell
winml build -c .\resnet50.json -m microsoft/resnet-50 --output-dir .\output
```

The configuration doesn't necessarily retain the source model path. Keep `-m` in scripts that replay a build.

Use either `--output-dir` for artifacts in a chosen directory or `--use-cache` for the persistent cache. They are mutually exclusive. The build saves the resolved configuration, including optimization changes, alongside its outputs.

Composite models can generate multiple configurations and output graphs. Use the reported component names and paths; use `--submodel` when you only need one supported component.

## Overrides at run time

Supported CLI overrides apply to the current invocation without editing the input file:

```powershell
winml build -c .\resnet50.json -m microsoft/resnet-50 -o .\baseline --no-quant --no-compile
```

- `--no-quant`, `--no-compile`, and `--no-optimize` disable their respective stages.
- `--no-analyze` skips the analyzer feedback loop; it doesn't establish compatibility.
- `--rebuild` reruns a build rather than reusing existing artifacts.

Enabling compilation isn't symmetric with disabling it: `build -c ... --compile` requires a non-null compile section. Generate that section with `config --compile` for a compatible EP first. Without a configuration file, `build` generates one and leaves compilation off unless requested.

`config` and `build` accept bare EP names, not the `provider@source` syntax supported by some runtime commands.

## Why version a config

Commit the recipe so changes to export shapes, optimization, calibration, and target can be reviewed. Also record:

- CLI and dependency versions.
- Model and dataset revisions.
- Representative calibration and evaluation inputs.
- Runtime, EP, driver, and target hardware.

These dependencies affect the output. A configuration alone doesn't guarantee byte-identical artifacts or compatibility across machines.

## See also

- [Primitives and pipeline](primitives-pipeline.md)
- [Config command](../commands/config.md)
- [Build command](../commands/build.md)
- [Output layout](../reference/output-layout.md)
