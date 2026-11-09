---
title: Use Windows ML CLI from Python workflows
description: Explore Windows ML CLI Python library workflows for model preparation and inference, with configuration and artifact-management guidance.
author: GrantMeStrength
ms.author: jken
ms.topic: how-to
ms.date: 10/29/2026
---

# Use Windows ML CLI from Python

The Windows ML CLI package also provides Python helpers for model preparation and inference experiments. Use the same isolated Python environment as the CLI so package versions and provider availability remain consistent.

This article describes a usage workflow, not the Windows ML runtime API reference. For app integration, see [Get started with Windows ML](../../get-started.md). For version-specific library definitions, see the [Windows ML CLI source](https://github.com/microsoft/winml-cli/tree/main/src/winml/modelkit).

## Prepare an artifact with the CLI

Use a configuration and explicit target to prepare a baseline:

```powershell
winml config -m microsoft/resnet-50 --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\resnet_config.json
winml build -c .\resnet_config.json -m microsoft/resnet-50 -o .\resnet_output
```

Keep the final model and referenced external weights together.

## Load the prepared model

Load that graph without rebuilding it:

```python
from winml.modelkit.models import WinMLAutoModel

model = WinMLAutoModel.from_onnx(
    "resnet_output/model.onnx",
    task="image-classification",
    device="cpu",
    ep="cpu",
    skip_build=True,
)
```

`skip_build=True` matters: the library's ONNX loading workflow can otherwise prepare the model before loading it. Don't assume Python helper defaults are identical to `winml perf` defaults.

Prepare inputs with the model's matching processor. Input names, shapes, and dtypes must match the exported graph. A randomly generated tensor can exercise a session but doesn't measure classification quality.

## Build from a supported checkpoint

`WinMLAutoModel.from_pretrained` can prepare and load a supported Hugging Face model. It can download configuration, weights, and preprocessing assets and can run enabled preparation stages, so account for those costs separately from inference.

Choose the task and target explicitly, and supply a reviewed build configuration for repeatable workflows. Keep remote model code disabled unless you trust its source.

## Composite models and bundles

Composite workflows need all cooperating components and model configuration, not just one arbitrary ONNX path. See the [CLIP sample](../samples/clip-composite.md) for loading separate encoder graphs.

A GenAI bundle has a distinct runtime configuration and tokenizer. Use the [Qwen3 bundle workflow](../samples/generative-model-bundle.md) rather than treating it as a single image- or text-classification graph.

## Validate and package

Keep preparation and validation separate:

- Use `winml eval` to compare task quality or reference outputs.
- Use `winml perf` to record performance under explicit measurement conditions.
- Preserve external tensors, context files, and bundle assets during packaging.
- Record the package version and model revision with the configuration.

For production app deployment, follow the Windows ML runtime guidance rather than assuming the CLI's Python preparation environment is your application runtime.

## See also

- [Build configuration](index.md)
- [Output layout](output-layout.md)
- [Evaluate quality](../commands/eval.md)
- [Measure performance](../commands/perf.md)
