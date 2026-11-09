---
title: "Weight and Activation with Windows ML CLI"
description: Understand how model weights and activations differ, why calibration matters, and how their precision affects Windows ML deployment.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Weight and Activation

Every neural network model stores two kinds of numeric tensors that matter for deployment: **weights**, the static parameters baked in at training time, and **activations**, the intermediate values that flow through the graph at every inference call. Understanding the distinction is the key to reading Windows ML CLI's precision flags, deciding when quantization is safe, and knowing why a model that runs fine on one execution provider may stall or degrade on another.

## Weights are static

Weights are the trained parameters of the model: convolution kernels, linear projection matrices, attention weights, embedding tables, bias vectors. They are fixed at the moment the model is exported and stay constant for every inference call. Because they are static, their quantization parameters — the scale and zero-point used to compress them from fp32 to int8 — can be computed from the weight values offline without representative input data. `winml quantize` does exactly that: it observes the weight distributions in your exported ONNX and bakes the per-tensor scale/zero-point into the QDQ nodes that wrap the weights.

In ONNX terms, weights are stored as **initializers** inside the graph. The runtime treats them as graph inputs that are always pre-supplied; you do not pass weights to a session at inference time, the way you pass an image tensor or a text prompt.

## Activations are dynamic

Activations are the intermediate results that flow through the graph during inference: the output of every matrix multiply, every layer norm, every attention softmax. Unlike weights, activations are regenerated on every forward pass and depend entirely on the input data. For static quantization, calibration runs representative inputs through the model and observes activation ranges; those estimates become the stored quantization parameters. Dynamic quantization computes activation scales at runtime instead. Weight-only quantization leaves activations in floating point.

This is why calibration data matters. If the calibration set fails to represent the inputs you will see in production, the per-activation ranges will be wrong and the quantized model will lose more accuracy than necessary on real traffic.

## Why they need separate flags

The `--weight-type` and `--activation-type` flags on `winml quantize` exist because the optimal bit-width for weights is not necessarily the optimal bit-width for activations:

- **Wider activation types** (int16 vs int8) reduce accuracy loss at the cost of more memory bandwidth. Useful when activations have heavy-tailed distributions that quantize poorly at 8 bits.
- **Narrower weight types** compress the static footprint more aggressively. Useful when the model is memory-bound and accuracy headroom exists.
- **Execution providers diverge** along this boundary too. QNN on NPU pairs uint8 weights with uint8 or uint16 activations. DirectML on GPU can run float16 throughout. CPU kernel support also depends on the operator and dtype; it isn't a universal fallback for every precision.

The compound precision shorthand `w8a16` (8-bit weights, 16-bit activations) reflects this asymmetry directly: weights and activations get different bit-widths in one config string. For the full precision family and how each maps to weight/activation dtypes, see [Datatype and Quantization](quantization.md).

## See also

- [Datatype and Quantization](quantization.md)
- [EP and Device](execution-providers-devices.md)
- [quantize command](../commands/quantize.md)
- [Graph and IR](model-graphs.md)
