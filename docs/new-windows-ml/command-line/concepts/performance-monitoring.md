---
title: Measure model performance with Windows ML CLI
description: Measure model latency, throughput, loading memory, and device activity with Windows ML CLI while keeping benchmark conditions comparable.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Perf and monitoring

Use `winml perf` to measure model execution on a selected device. Performance measurements complement accuracy evaluation; they don't replace it.

## What perf measures

For a single ONNX model, the benchmark excludes warm-up iterations and reports latency statistics and throughput. A saved report identifies the runtime, target, model, and measurement settings.

```powershell
winml perf -m .\model.onnx --device cpu --ep cpu --warmup 10 --iterations 50 -o .\perf_cpu.json
```

Use a prebuilt model to separate model preparation from benchmarking. Hugging Face model identifiers can trigger a build before measurement. A timing result isn't the end-to-end latency of your application: preprocessing, application logic, and presentation can add work outside the measured scope.

### Output format

Use `--output` to save JSON, or `--format json` for structured stdout. Don't parse console tables as a stable data format.

Keep the report's runtime and schema version when storing results. ONNX inference, GenAI generation, and experimental GGUF workflows have different timing definitions. Compare like-for-like reports, not just similarly named fields.

## Choose inputs and duration

Generated inputs are useful for an initial performance baseline. Use `--input-data` to benchmark representative tensors in a NumPy `.npz` archive. Its keys must match model inputs, and the benchmark runs the archive as one batch; this differs from evaluation compare mode's sample-axis convention.

Use `--iterations` for a fixed sample count. For a supported non-tracing workflow, `--duration` requests a minimum measurement time after warm-up:

```powershell
winml perf -m .\model.onnx --device cpu --ep cpu --duration 10 --monitor -o .\perf_duration.json
```

The last inference can finish after the requested duration. A longer run also gives sampled hardware counters time to produce readings. `--duration` isn't valid with `--op-tracing`.

## Live monitoring

`--monitor` samples available CPU, RAM, accelerator utilization, and device-memory metrics. Availability depends on the runtime, driver, permissions, and hardware.

Monitoring can add system overhead even if display updates aren't inside the timed inference call. Keep monitoring settings consistent between comparisons. Low device utilization doesn't by itself prove that inference fell back to the CPU; inspect placement and profiling data as well.

## Memory and resource metrics

Distinguish model-loading measurements from steady-state inference:

- **Loading memory** describes supported measurements around model/session preparation.
- **Inference memory** describes resource observations during the benchmark.
- **Peak and delta values** depend on the baseline and sampling window. A sampled peak isn't necessarily the process's lifetime peak.

Unavailable counters must remain unavailable, not be interpreted as zero. Negative deltas can occur when allocations are released between measurements.

For the report fields and lifecycle boundaries, see the [perf command reference](../commands/perf.md).

## Select an adapter and investigate operators

Use `winml sys` to identify physical adapters. On supported `winml-ort` EP/device pairs, `--device-luid` pins inference and monitoring to the selected adapter. It doesn't apply to `ort-genai` and can't be combined with an EP `device_id` override.

Use `--op-tracing basic` or `--op-tracing detail` where supported to investigate operator timing. Provider-specific tracing can require compilation, additional artifacts, or a different measurement path. Profiling results aren't directly interchangeable with an unprofiled benchmark.

For supported model workflows, `--module` or `--submodel` can narrow measurement to a module or composite component. Those measurements don't represent the latency of the full application pipeline.

## Generation metrics

For an ONNX Runtime GenAI bundle, `--runtime ort-genai` reports time to first token and decode throughput. Record the prompt, prompt template, output-token limit, device routing, and runtime settings when comparing results.

Experimental GGUF generation uses Windows ML Tasks and has separate timing semantics. See the [GGUF sample](../samples/language-model-benchmark.md) before comparing those results with GenAI or ONNX inference.

## Make comparisons repeatable

Keep the model revision, input shapes and data, precision, batch size, runtime, driver, hardware target, power settings, and warm-up policy consistent. Record whether the model was built from cache or rebuilt. Use multiple measurements rather than relying on a single unusually fast run.

## See also

- [Perf command](../commands/perf.md)
- [Evaluate quality](evaluation-datasets.md)
- [Execution providers and devices](execution-providers-devices.md)
