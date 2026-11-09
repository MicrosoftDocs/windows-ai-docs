---
title: "Windows ML CLI command reference"
description: Choose Windows ML CLI commands for model discovery, configuration, preparation, performance measurement, and quality evaluation workflows.
author: GrantMeStrength
ms.author: jken
ms.topic: reference
ms.date: 10/29/2026
---

# Commands

Windows ML CLI exposes a CLI named `winml` with 12 subcommands covering the full
journey from model discovery to a deployment-ready artifact. Every subcommand
shares a consistent invocation style — `winml <command> [flags]` — and the
same global flags are available on the root `winml` group.

The commands group by user intent. **Discover** (`sys`, `inspect`, `catalog`,
`analyze`) helps you understand your hardware and model before writing any
artifacts. **Configure** (`config`, `optimize`) produces a reusable build
configuration and tunes the ONNX graph. **Build** (`export`, `quantize`,
`compile`, `build`) runs the pipeline stages that produce deployment artifacts.
**Measure** (`perf`, `eval`) benchmarks and validates the result.

The typical workflow follows that order: run `winml sys` to confirm hardware
and EPs, then `winml inspect` or `winml catalog` to verify model support. Use
`winml config` to generate a build configuration, then `winml build` to execute
the full pipeline — or chain `export` → `analyze` → `optimize` → `quantize` → `compile`
individually for finer control. Close with `winml perf` and `winml eval` to
measure speed and accuracy.

## Command map

| Command | Group | Purpose |
|---|---|---|
| [`sys`](sys.md) | Discover | Inspect your machine — devices, EPs, and runtime versions at a glance. |
| [`inspect`](inspect.md) | Discover | Inspect a model's tasks, classes, and hierarchy before committing to an export. |
| [`catalog`](catalog.md) | Discover | Browse the curated Windows ML CLI catalog of validated models and benchmarks. |
| [`config`](config.md) | Configure | Generate a reusable build configuration for a Hugging Face model or ONNX file. |
| [`export`](export.md) | Build | Convert a PyTorch / Hugging Face model to ONNX, preserving module hierarchy. |
| [`analyze`](analyze.md) | Build | Inspect known operator and pattern support for a target execution provider. |
| [`optimize`](optimize.md) | Build | Apply graph optimizations and fusions to an ONNX model to reduce node count and improve inference speed. |
| [`quantize`](quantize.md) | Build | Apply FP16, INT4 weight-only, dynamic, or calibrated static QDQ conversion. |
| [`compile`](compile.md) | Build | Compile an ONNX model to an EP-specific format for fast runtime loading. |
| [`build`](build.md) | Build | Run the entire Windows ML CLI pipeline (export → optimize → quantize → compile) in one command. |
| [`perf`](perf.md) | Measure | Benchmark an ONNX model's latency and throughput on a target device. |
| [`eval`](eval.md) | Measure | Evaluate task quality or compare outputs with a reference, using supported ONNX and PyTorch workflows. |

## Choosing a command

- **I want to see what hardware and EPs I have** → `winml sys`
- **I want to know if my model is supported** → `winml inspect`
- **I want to browse validated models with known benchmarks** → `winml catalog`
- **I want to verify EP operator compatibility before compiling** → `winml analyze`
- **I want to convert a Hugging Face model to ONNX** → `winml export`
- **I want to run the whole pipeline in one go** → `winml build`
- **I want to benchmark latency and throughput** → `winml perf`
- **I want to measure model accuracy** → `winml eval`

## Global flags

Use `winml --version` to check the installed version and `winml --help` to list
commands. Use `winml <command> --help` to inspect a command's flags. Verbosity
options are also exposed by individual commands; check their help rather than
assuming every option has the same placement.

## Shared flags

Most file-producing commands reject existing outputs unless you pass `--overwrite`.
`build` uses `--rebuild` instead. The `-o` value can mean a file or directory
depending on the command; use the individual reference rather than copying a path
unchanged between stages.

Several flags share semantics across the commands that accept them:
`-m` / `--model`, `-d` / `--device`, `--ep`, `-o` / `--output`,
`-t` / `--task`, and `--precision`. Defaults and accepted values can
differ per command; check the **Flags** section of each command page
rather than assuming they transfer.

`--no-color` is accepted by every command and disables colored output for
that invocation. Color is also auto-disabled when `NO_COLOR=1` or `CI=true`
is set in the environment.

## See also

- [How Windows ML CLI Works](../concepts/how-it-works.md) — end-to-end pipeline overview
- [Config and build](../concepts/configuration-build.md) — structure of `WinMLBuildConfig` and how stages interact
- [ONNX & Execution Providers](../concepts/execution-providers-devices.md) — background on EPs and how `--device` / `--ep` interact
- [winml build](build.md) — the single command that runs the entire pipeline
