---
title: Model precision and quantization in Windows ML CLI
description: Choose floating-point, static, dynamic, or weight-only precision workflows in Windows ML CLI and validate accuracy on representative data.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Model precision and quantization

A model's numerical precision is the format used to represent its values; it affects size, memory use, operator support, and accuracy. Quantization represents values with fewer bits, often as integers, to reduce model size or computation cost. Windows ML CLI supports floating-point conversion and several quantization workflows. Choose a precision supported by your model and target [execution provider (EP)](execution-providers-devices.md), then measure the result.

## Datatypes

Build commands accept `--precision` to configure model preparation. Common choices include:

| Precision | Intended use |
|---|---|
| `fp32` | Keep a 32-bit floating-point baseline. |
| `fp16` | Use 16-bit (half-precision) floating point where supported by the preparation path and target EP. |
| `int8` or `w8a8` | Use 8-bit weights and activations for static quantization. |
| `int16` | Use a wider integer quantization configuration. |
| `w8a16` | Use 8-bit weights and 16-bit activations. |
| `auto` | Let configuration resolve a target-dependent precision. Current target defaults are `w8a16` for NPU and `fp16` for CPU/GPU; model recipes can impose additional constraints. |

Don't infer the precision of an existing artifact from a flag used only for model building. Inspect the generated configuration and model, especially when a stage is skipped.

The standalone [quantize command](../commands/quantize.md) also supports `int4` weight-only quantization, `dynamic` quantization, and repeated `--precision` options. These standalone modes aren't interchangeable with the precision choices accepted by every build or inference command.

## How quantization works in Windows ML CLI

### Static QDQ quantization

Static quantization uses calibration inputs to estimate activation ranges. The resulting ONNX graph contains QuantizeLinear/DequantizeLinear (QDQ) nodes and associated scale and zero-point values.

The EP decides which QDQ patterns it can execute efficiently. A standard ONNX representation doesn't guarantee support for every combination of operators, weight types, and activation types.

### Calibration

Use data representative of the model's intended workload. For supported tasks, `--task` and `--model-id` select the default calibration dataset and matching preprocessor. If task-based data isn't available, generated input tensors can help exercise the pipeline but aren't a production-quality accuracy check.

The calibration methods are:

- `minmax`: Use the observed minimum and maximum values.
- `entropy`: Estimate ranges using distribution information.
- `percentile`: Clip extreme values before estimating ranges.

`--samples` controls calibration sample count. More samples aren't a substitute for representative input distributions. Compare task metrics when choosing a method or increasing the sample count.

### Dynamic and weight-only quantization

Dynamic quantization doesn't require a calibration dataset; activation quantization parameters are calculated during execution. Round-to-nearest (RTN) weight-only quantization reduces weight precision without the same activation calibration step. Both depend on runtime and operator support and should be evaluated on the intended hardware.

### Floating-point conversion

FP16 conversion changes the range and precision of floating-point values without inserting QDQ nodes. It can introduce overflow, underflow, or accuracy changes. It isn't lossless.

## Choose and validate a precision

1. Establish an FP32 baseline using representative inputs and task metrics.
2. Check the target EP's supported operators and precision types.
3. Prepare a lower-precision candidate using representative calibration data when required.
4. Compare outputs and task accuracy with the baseline.
5. Measure latency, throughput, and memory on the intended hardware.

Keep model revisions, data, shapes, and runtime settings consistent between comparisons. A smaller model isn't necessarily faster if unsupported operators cause fallback or additional conversions.

## See also

- [Weights and activations](weights-activations.md)
- [Execution providers and devices](execution-providers-devices.md)
- [Quantize command reference](../commands/quantize.md)
- [Evaluation command reference](../commands/eval.md)
