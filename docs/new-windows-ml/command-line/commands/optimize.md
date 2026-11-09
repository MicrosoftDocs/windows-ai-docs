---
title: "Windows ML CLI optimize command"
description: Optimize ONNX graphs with Windows ML CLI, discover available capabilities, apply analyzer recommendations, and preserve comparison baselines.
author: GrantMeStrength
ms.author: jken
ms.topic: reference
ms.date: 10/29/2026
---

# winml optimize

> Apply graph optimizations and fusions to an ONNX model to reduce node count and improve inference speed.

## When to use this

Use `winml optimize` after exporting an ONNX model and before quantization or compilation. Graph fusions reduce operator count, improve memory locality, and can make downstream quantization more accurate by presenting cleaner subgraphs to the calibration pass. It is also useful as a standalone step when you want to optimize a pre-exported ONNX file without running the full build pipeline.

## Synopsis

```powershell
winml optimize [options]
```

## Flags

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--model` | `-m` | `PATH` | *(required unless listing)* | Input ONNX model file. Not required when `--list-capabilities` or `--list-rewrites` is used. |
| `--output` | `-o` | `PATH` | `{input}_opt.onnx` | Output path for the optimized model. Defaults to the input filename with `_opt` inserted before the extension. |
| `--config` | `-c` | `PATH` | *(none)* | YAML or JSON configuration file. Fields in the file override capability defaults; CLI flags override the file. |
| `--ep`, `--device` | | target | CPU when omitted | Select the EP/device used to determine compatible optimization capabilities. |
| `--overwrite` | | flag | `false` | Replace an existing output model. |
| `--enable-ort-graph-optimization` / `--disable-ort-graph-optimization` | | flag | enabled | Run or skip ORTGraphPipe. Disabling it does not implicitly enable compatibility rewrites or disable other pipes. Config key: `ort-graph-optimization` (boolean). |
| `--verbose` | `-v` | flag | off | Enable verbose output. |
| `--list-capabilities` | `-l` | flag | off | Print all registered optimization capabilities grouped by category and exit. Add `--verbose` for descriptions and ORT names. |
| `--list-rewrites` | | flag | off | Print all available pattern-rewrite families with their source-to-target mappings and exit. |
| `--check-optim` | | flag | off | Analyze which optimizations would apply to `--model` and the nodes/constants they affect, then exit **without writing any output**. Probes every capability independently. |
| *(dynamic)* | | flag | *(per capability)* | Each registered capability generates a `--enable-<name>` / `--disable-<name>` pair. Run `--list-capabilities` to see the full current list. Examples: `--enable-gelu-fusion`, `--disable-constant-folding`. Pattern-rewrite flags follow the form `--enable-<source-slug>-<target-slug>`; run `--list-rewrites` to discover all names. |

### Configuration precedence

When multiple sources are provided, settings are resolved in this order (highest wins):

1. Explicit CLI flags (`--enable-X` / `--disable-X`)
2. Config file (`-c`)
3. Capability defaults

## How it works

`winml optimize` loads the ONNX model, builds a final capability configuration by merging capability defaults, an optional config file, and any explicit CLI flags, then runs all enabled passes through the `Optimizer`. Each capability maps to a named optimization or fusion pipe in the `winml.modelkit.optim` registry. The capability flags are auto-generated at startup from that registry — adding a new optimization to the registry automatically makes it available as a CLI flag without any change to this command's source. After optimization, the command prints the before-and-after node count and percentage reduction so you can quantify the effect.

## Examples

Optimize a model with all capability defaults:

```powershell
winml optimize -m microsoft/resnet-50.onnx
```

```text
Input:  microsoft/resnet-50.onnx
Output: microsoft/resnet-50_opt.onnx

Loading model...
Running optimizer...
Saving optimized model...

Success! Model optimized: microsoft/resnet-50_opt.onnx
Nodes: 312 -> 289 (7.4% reduction)
```

Enable specific fusions for a BERT model:

```powershell
winml optimize -m bert-base-uncased.onnx `
    --enable-layer-norm-fusion `
    --enable-attention-fusion `
    -o bert_layernorm_attn.onnx
```

Use a config file to set capabilities and save the result for downstream compilation:

```powershell
winml optimize -m facebook/convnext-tiny-224.onnx `
    -c optimize_config.yaml `
    -o convnext_opt.onnx
```

List all available optimization capabilities:

```powershell
winml optimize --list-capabilities
```

Discover pattern-rewrite families and their flag names:

```powershell
winml optimize --list-rewrites
```

Check which optimizations apply to a model before committing to a run (writes nothing):

```powershell
winml optimize -m bert-base-uncased.onnx --check-optim
```

```text
Input: bert-base-uncased.onnx
--check-optim — analyzing applicable optimizations (no output written).

Loading model...
Probing 61 optimization capabilities... (this can take a while on large models)

2 applicable optimization(s):

--enable-matmul-add-fusion  (matmul)
  Fuse MatMul+Add operations into single kernel
  nodes: -1 ~1
    removed: MatMul (1)
      - MatMul 'mm'
    modified: Gemm (1)
      - Gemm 'mm/MatMulAddFusion'

--enable-clamp-constant-values  (surgery)
  Clamp extreme float constants (e.g., -inf -> -1e3) to prevent quantization issues
  constants: ~1
      - BIG

Enable any of the above with its --enable-* flag (dependencies are auto-enabled).
```

Add `-v` to list every affected node and constant instead of a sample.

## Common pitfalls

- **`--model` is required for actual optimization** — it can be omitted only when using `--list-capabilities` or `--list-rewrites`. Missing `--model` in any other case raises a usage error.
- **Config file and CLI flags interact via precedence** — a `--disable-X` CLI flag always wins over a config file value that enables the same capability, but omitting the flag leaves the config file value in effect. To turn off a capability set by a config file, pass the explicit `--disable-X` flag.
- **Config file validation errors abort the run** — if the config file contains keys that fail capability validation or dependency checks, the command prints all errors and exits with code 1 without touching the model. Fix the config before retrying.
- **The dynamic flag list changes between releases** — new capabilities are added as the optimizer registry grows. Always use `--list-capabilities` to confirm the current set of flags rather than relying on a cached list.
- **Existing outputs are protected** — use a different `-o` path or explicitly pass `--overwrite` when replacing a candidate. Keep the baseline model for accuracy comparisons.

## See also

- [how-it-works.md](../concepts/how-it-works.md) — where optimization fits in the full Windows ML CLI pipeline
- [export.md](export.md) — produce an ONNX file to optimize from a HuggingFace model
- [quantize.md](quantize.md) — quantize the optimized model for lower-precision inference
- [config.md](config.md) — generate a `WinMLBuildConfig` that includes optimization settings
