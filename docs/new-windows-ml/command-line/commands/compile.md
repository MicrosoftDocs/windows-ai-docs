---
title: "Windows ML CLI compile command"
description: Compile a prepared ONNX model for a compatible execution provider, select context storage, and share context with Windows ML CLI.
author: GrantMeStrength
ms.author: jken
ms.topic: reference
ms.date: 10/29/2026
---

# winml compile

> Compile ONNX to an EP-specific format.

## When to use this

Use `winml compile` as the final pipeline stage after `winml quantize` to
produce an execution-provider-native artifact (for example, a QNN EPContext
model) that loads faster and avoids online graph compilation at inference time.

## Synopsis

```powershell
winml compile [options]
```

## Flags

| Flag | Short | Type | Default | Description |
|---|---|---|---|---|
| `--model` | `-m` | path | *(required unless `--list`)* | Input ONNX model. Repeat for shared-context multi-model compilation. |
| `--output` | `-o` | path | — | Output file path (e.g., `model_compiled.onnx`). Takes precedence over `--output-dir`. |
| `--output-dir` | | path | same dir as input | Directory to write compiled output artifacts. |
| `--device` | `-d` | choice | inferred | Infer from the EP, or use NPU when neither EP nor device is given. Specify the intended device explicitly. |
| `--overwrite` | | flag | `false` | Replace an existing output artifact. |
| `--config` | `-c` | path | none | Read compile defaults from a `WinMLBuildConfig` JSON file. |
| `--ep` | | `TEXT` | — | Force a specific execution provider, overriding device-to-provider mapping. Accepts full names (e.g., `QNNExecutionProvider`) or aliases (`qnn`, `dml`, `openvino`, `vitisai`, `migraphx`, `cpu`, `nvtensorrtrtx`). |
| `--ep-options` | | `KEY=VALUE` | — | Execution-provider option for the compilation session. Repeat for multiple options; later duplicate keys win. Explicit CLI values override matching `compile.provider_options` keys from `--config`. |
| `--validate` / `--no-validate` | | flag | `--validate` | Post-compilation inference validation. |
| `--compiler` | | choice | `ort` | Backends: `ort`, `ort_session`, `qairt`. |
| `--qnn-sdk-root` | | path | `None` | Path to the QNN SDK root directory. |
| `--embed/--no-embed` | | flag | `false` | Embed the EP context blob inside the ONNX file instead of writing a separate `.bin` file. |
| `--list` | | flag | `false` | List available compiler backends for the selected device and exit without compiling. |
| `--help` | `-h` | flag | | Show this message and exit. |

## How it works

`winml compile` resolves the target execution provider from `--device` and
`--ep`, then calls the Windows ML CLI compiler API to hand the ONNX graph to the
EP's offline compilation toolchain. If neither target is given, the command
targets NPU; specify the EP and device for repeatable selection. QNN can write
an external context or embed it with `--embed`. Other providers have their own
context layouts and requirements.

The default validation pass runs inference on the compiled model. `--no-validate`
skips that check, but doesn't remove hardware, driver, or SDK requirements of the
compiler itself.

If a provider option names a compiler input file, list its key in
`compile.provider_option_file_keys` in the JSON config. The CLI canonicalizes
that option path and fingerprints its contents for EPContext cache identity;
other provider-option strings are always passed through unchanged.

### Shared context for multiple models

For compatible models and providers, repeat `-m` to share compiled context and weights. Use `--compiler ort_session` and an output directory; a single `-o` file isn't valid for multi-model compilation.

```powershell
winml compile -m .\context.onnx -m .\iterator.onnx --ep qnn --device npu --compiler ort_session --output-dir .\compiled
```

Keep the resulting ONNX files and shared context files together. Context sharing and model compatibility are provider-specific.

`compile` also accepts source-qualified EP names such as `openvino@pypi` when that source is available. Check the runtime environment before selecting a source; `config` and `build` don't accept this syntax.

## Examples

```powershell
# Compile for a compatible Qualcomm NPU
winml compile -m resnet50_qdq.onnx --ep qnn --device npu
```

Use the reported output paths and retain every context or external-weight file
referenced by the compiled model.

```powershell
# List available compiler backends for NPU before committing to a run
winml compile --list --device npu
```

```powershell
# Compile a pre-quantized BERT model for NPU with context embedded inline
winml compile -m bert-base-uncased_qdq.onnx --embed
```

```powershell
# Pass provider-specific options to the compilation session
winml compile -m model.onnx --ep qnn `
  --ep-options htp_performance_mode=burst `
  --ep-options soc_model=57
```

```powershell
# Compile for GPU using the OpenVINO execution provider
winml compile -m microsoft_resnet50.onnx --device gpu --ep openvino
```

## Common pitfalls

- **Embedding isn't universal portability.** It moves the context into the ONNX
  file but doesn't remove external tensor dependencies or provider requirements.
- **Validation requires the target hardware.** The post-compilation validation
  step runs an actual inference pass; on a machine without the NPU driver or the
  relevant EP installed, validation can't establish deployability. Skipping it
  doesn't guarantee that compilation itself works without that hardware.
- **`--device auto` auto-detects the best available hardware.** Pass `--device npu`,
  `--device gpu`, or `--device cpu` explicitly when targeting specific hardware
  regardless of what is auto-detected.

## See also

- [winml quantize](quantize.md)
- [winml build](build.md)
- [ONNX and execution providers](../concepts/execution-providers-devices.md)
