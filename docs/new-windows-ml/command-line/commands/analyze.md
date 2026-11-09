---
title: "Windows ML CLI analyze command"
description: Inspect ONNX operator support for execution providers, save compatibility reports, and generate optimization settings with Windows ML CLI.
author: GrantMeStrength
ms.author: jken
ms.topic: reference
ms.date: 10/29/2026
---

# winml analyze

Inspect ONNX operator and pattern support for a target execution provider before runtime validation.

## When to use this

Use `winml analyze` to identify operator gaps and optimization recommendations. Static rules describe known support, not a guarantee that the complete model compiles or meets performance and accuracy requirements.

## Synopsis

```powershell
winml analyze [options]
```

## Flags

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--model` | `-m` | `PATH` | *(required)* | Path to the ONNX model file to analyze. |
| `--ep` | | choice | `auto` | Target execution provider. Accepts full names (e.g., `QNNExecutionProvider`) or short aliases (`qnn`, `openvino`, `vitisai`, `cpu`, `dml`, `nv_tensorrt_rtx`, `migraphx`). Use `all` for every rule-data-backed EP, or `auto` to infer from local availability. |
| `--device` | | `cpu\|gpu\|npu\|all\|auto` | `auto` | Target device type. `auto` infers from local availability; `all` evaluates all rule-data-backed devices. |
| `--verbose` | `-v` | flag | off | Enable verbose output. |
| `--quiet` | `-q` | flag | off | Suppress non-essential output. |
| `--config` | `-c` | `PATH` | *(none)* | `WinMLBuildConfig` JSON file. |
| `--format` | `-f` | `text\|json` | `text` | Print a human-readable report or structured JSON to stdout. |
| `--overwrite` | | flag | `false` | Replace existing `--output` and `--optim-config` files. |
| `--output` | | `PATH` | *(none)* | Save the full JSON result to a file in addition to printing the console summary. |
| `--information` / `--no-information` | | flag | enabled | Include detailed per-operator recommendations and remediation hints in the output. Pass `--no-information` for a compact pass/fail summary. |
| `--run-unknown-op` / `--no-run-unknown-op` | | flag | disabled | For operators not in the rule database, build a minimal ONNX graph and run it on the target EP locally to determine support. Enable when local EP libraries are available. |
| `--save-node` | | `partial\|unsupported` | *(none)* | Save partial or unsupported node subgraphs to disk for further investigation. Can be specified multiple times: `--save-node partial --save-node unsupported`. |
| `--optim-config` | | `PATH` | *(none)* | Save the auto-discovered optimization config (merged across all analyzed EPs) to a JSON file. |
| `--check-optim` / `--no-check-optim` | | flag | disabled | For each optimization that would change the model, check whether the operators it *introduces* (its added/modified nodes) are supported on the resolved EP/device. Answers "if I apply this optimization, will its output run on my target?" before you commit to it. |

## How it works

`winml analyze` loads the ONNX model and runs a static analysis pass via `ONNXStaticAnalyzer`. For each operator (and recognized subgraph pattern), the analyzer consults the target EP's rule database. For operators not in the database, it can optionally probe them locally when `--run-unknown-op` is enabled. The combined answer classifies each node as supported, partial, unsupported, or unknown (see [Analyze and optimize](../concepts/analysis-optimization.md) for definitions).

The analysis always produces a **lint** result — the pass/fail verdict. When `--information` is enabled (the default), it additionally produces an **autoconf** result: a set of fusion-flag suggestions that, if applied in the optimize stage, would resolve partial or unsupported patterns. Pass `--no-information` to skip autoconf and get just the lint verdict.

### Exit codes

| Code | Meaning |
|------|---------|
| `0`  | All operators are fully supported on the target EP. |
| `1`  | At least one operator is unsupported, partially supported, or unknown. |
| `2`  | Input or configuration error (bad path, unknown EP, etc.). |

Use the exit code together with the saved findings in automation. In PowerShell, `$LASTEXITCODE` holds the native command's numeric exit code; `$?` is a Boolean success indicator.

## Examples

Analyze using auto-detected EP and device:

```powershell
winml analyze --model microsoft/resnet-50.onnx
```

The text report includes per-target findings and support counts for supported, partial, unsupported, and unknown nodes. Prefer `--format json` or `--output` for scripts rather than parsing console layout.

Check QNN NPU support using the short alias:

```powershell
winml analyze --model bert-base-uncased.onnx --ep qnn --device NPU
```

Check Intel OpenVINO GPU support and print operator-level recommendations:

```powershell
winml analyze --model bert-base-uncased.onnx --ep openvino --device GPU --information
```

Save the full JSON result for offline inspection while still printing the console summary:

```powershell
winml analyze --model facebook/convnext-tiny-224.onnx --output results.json
```

Run a lint-only pass (no recommendations) for a CI gate:

```powershell
winml analyze --model model.onnx --ep qnn --device NPU --no-information
Write-Output "Exit code: $LASTEXITCODE"
```

Dump unsupported subgraphs to disk for debugging:

```powershell
winml analyze --model model.onnx --ep qnn `
    --save-node partial --save-node unsupported `
    --output result.json
```

Enable local execution for operators not in the rule database:

```powershell
winml analyze --model model.onnx --ep qnn --device NPU --run-unknown-op
```

Check whether the operators each applicable optimization would introduce are supported on the target:

```powershell
winml analyze --model model.onnx --ep qnn --device NPU --check-optim
```

## Common pitfalls

- **Omitting `--ep` uses `auto` (inferred from local availability)** — to analyze every EP regardless of what is installed, pass `--ep all`. Specify `--ep <name>` when you know your target hardware.
- **Exit code 1 is not a hard failure** — it means at least one operator is unsupported, not that the model cannot run at all. Many EPs fall back unsupported nodes to the CPU EP automatically; review the recommendations before deciding to restructure the model.
- **`--run-unknown-op` is disabled by default** — operators not covered by the rule database are classified as `UNKNOWN` (not unsupported) unless you explicitly pass `--run-unknown-op` to probe them locally. Enable it only when the target EP's libraries are available on the local machine.
- **The model path must point to an existing `.onnx` file** — symbolic HuggingFace model IDs are not accepted; export the model first with `winml export`.

## See also

- [Analyze and optimize](../concepts/analysis-optimization.md) — conceptual deep dive on classifications, lint vs autoconf, and the analyzer/optimizer loop
- [Execution providers and devices](../concepts/execution-providers-devices.md) — background on ONNX operators and execution providers
- [export.md](export.md) — convert a HuggingFace model to ONNX before analyzing
- [compile.md](compile.md) — compile the model for the target EP after analysis passes
- [sys.md](sys.md) — list EPs available on the current machine
