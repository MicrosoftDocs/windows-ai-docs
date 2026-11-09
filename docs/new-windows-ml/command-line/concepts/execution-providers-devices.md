---
title: Select execution providers and devices in Windows ML CLI
description: Select a Windows ML execution provider, device category, provider source, and physical adapter for model preparation and measurement.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Execution providers and devices

An execution provider (EP) implements model operations for a runtime and hardware target. A device category identifies CPU, GPU, or NPU. Some EPs support multiple categories, so specify both when you need an unambiguous target.

Graph partitioning and fallback depend on the provider, model, and session settings. The presence of an EP doesn't guarantee support for every operator, dtype, or shape.

## EPs Windows ML CLI supports

| Provider | Alias | Typical target |
|---|---|---|
| `QNNExecutionProvider` | `qnn` | Qualcomm NPU; other targets depend on provider configuration. |
| `VitisAIExecutionProvider` | `vitisai` | AMD NPU. |
| `OpenVINOExecutionProvider` | `openvino` | Supported Intel CPU, GPU, or NPU. |
| `DmlExecutionProvider` | `dml` | Compatible DirectX 12 GPU. |
| `NvTensorRTRTXExecutionProvider` | `nv_tensorrt_rtx` | Supported NVIDIA GPU. |
| `MIGraphXExecutionProvider` | `migraphx` | Supported AMD GPU. |
| `CPUExecutionProvider` | `cpu` | CPU. |

Availability differs by installed runtime, driver, hardware, and command. For NPU workflows, use Windows 11, version 24H2 or later with a compatible provider and driver.

Inspect the local inventory:

```powershell
winml sys --list-device
winml sys --list-ep
```

## Device vs. EP on the CLI

Use `--device` for a category and `--ep` for a provider:

```powershell
winml perf -m .\model.onnx --device cpu --ep cpu
```

Automatic selection generally prioritizes NPU, then GPU, then CPU. It is a selection policy, not a measurement of which target is fastest for your model.

Defaults and accepted values differ:

- `analyze` accepts `all` to inspect available rule-backed targets. That doesn't require every target to be locally runnable.
- `compile` infers a device from the EP, or defaults to NPU when neither is supplied. CPU and DirectML don't provide offline EPContext compilation.
- `perf --runtime ort-genai --device config` preserves the bundle's existing device routing. Other perf runtimes don't accept `config`.

An explicit EP narrows the target, but it doesn't make a contradictory device choice valid. Use `winml <command> --help` for that command's accepted provider forms and restrictions.

## Provider sources

Commands that expose source-qualified EP selection, including `compile` and `perf`, accept values such as `openvino@pypi`. The suffix selects a provider source where the runtime supports it; it doesn't install arbitrary packages or create unsupported hardware capabilities.

`config` and `build` accept bare provider names, not `provider@source`. Don't transfer a source-qualified option between commands without checking its help.

## Physical adapters and provider options

For supported runtime workflows, `--device-luid` selects an adapter reported by `winml sys`. This matters when a machine has more than one device in the same category.

`perf --device-luid` isn't supported with `ort-genai` and can't be combined with `--ep-options device_id=...`. Evaluation offers separate candidate and reference adapter options.

Use repeatable `--ep-options KEY=VALUE` where supported for provider-specific settings. Their meaning and valid values belong to the provider; record them with benchmark results.

## Deployment implications

A graph optimized for one EP can contain operations unsupported by another. A compiled EPContext is more target-specific still. Keep the uncompiled baseline and validate precision, model quality, latency, and startup on each intended deployment environment.

## See also

- [Sys command](../commands/sys.md)
- [Analyze command](../commands/analyze.md)
- [Compile and EPContext](compilation-execution-provider-context.md)
- [Perf command](../commands/perf.md)
