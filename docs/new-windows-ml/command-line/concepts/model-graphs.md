---
title: Understand ONNX graphs and versions for Windows ML
description: Understand ONNX graphs, operator sets, intermediate representations, and execution-provider requirements when preparing models for Windows.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Graph and IR

An ONNX model represents a computation as a graph of operators connected by named tensors. Windows ML CLI analyzes and transforms that representation rather than the original framework's Python objects.

## What is in an ONNX file

An ONNX file serializes a `ModelProto` using Protocol Buffers. Its graph includes:

- **Inputs and outputs:** Named values with type and shape information.
- **Nodes:** Operators such as convolution, matrix multiplication, or normalization.
- **Initializers:** Constant tensors, often learned weights or biases.
- **Metadata:** Properties used by tools, including model I/O and source-hierarchy information.

Tensor data can be embedded or stored in external files. A compiled graph can also reference an external EP context. An `.onnx` file isn't necessarily self-contained; preserve the files it references.

## Graphs as intermediate representations

A framework-neutral graph helps tools inspect operator relationships, infer shapes where possible, and transform compatible patterns. The format doesn't guarantee that every runtime or execution provider implements every operator, domain, dtype, or shape.

Static graph analysis can work without executing the complete model. Calibration, runtime probing, compilation validation, and inference have additional data, runtime, and hardware requirements.

## Opsets and versioning

Keep these versions distinct:

- **IR version:** The ONNX model-format version.
- **Opset imports:** Operator definitions selected for each domain.
- **Runtime/provider version:** The implementation that must support the graph.

The CLI's default export configuration requests opset 17. Model-specific settings or exporter behavior can change the result. To request a different opset, create an export configuration:

```json
{
  "opset_version": 18
}
```

Save it as `export_config.json`, then export:

```powershell
winml export -m microsoft/resnet-50 -o .\resnet50.onnx --export-config .\export_config.json
```

Review exporter warnings and inspect the resulting model:

```python
import onnx

model = onnx.load("resnet50.onnx", load_external_data=False)
print("IR version:", model.ir_version)
for opset in model.opset_import:
    print(opset.domain or "ai.onnx", opset.version)
```

Higher opsets aren't automatically more compatible. Optimization and precision conversion can introduce different operators or version requirements, so inspect and validate the transformed artifact as well.

## See also

- [Execution providers and devices](execution-providers-devices.md)
- [Weights and activations](weights-activations.md)
- [Quantization](quantization.md)
- [Export command](../commands/export.md)
