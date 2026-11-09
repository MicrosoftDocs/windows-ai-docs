---
title: Load and export models with Windows ML CLI
description: Select a model task and ONNX exporter, control input shapes, preserve hierarchy metadata, and export components with Windows ML CLI.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Load and export

Export converts a supported source model into an [ONNX graph](model-graphs.md), a framework-independent description of its calculation steps, for later analysis, optimization, precision conversion, and deployment. Start with the correct task and input specification; a valid export alone doesn't guarantee compatibility with every [execution provider](execution-providers-devices.md), the runtime component that runs model operations on the target hardware.

## Loading a model

Use a [Hugging Face model ID](https://huggingface.co/docs/hub/models-the-hub) or a supported local checkpoint directory, which contains saved model files. Loading can require [weights](weights-activations.md) (the model's learned parameters), configuration, and tokenizer or processor files that prepare text, images, or other data as model inputs. It also requires network access for uncached resources.

Before export, use `winml inspect` to review the inferred task and model configuration. `--hierarchy` constructs and traces a random-weight model; it can still require configuration downloads and significant memory.

If task detection is ambiguous, pass `--task` explicitly. Only enable remote model code for a repository you trust, using the corresponding loader setting in a configuration generated with `winml config`.

## Exporting to ONNX

```powershell
winml export -m microsoft/resnet-50 --task image-classification -o .\resnet50.onnx
```

[PyTorch](https://docs.pytorch.org/docs/stable/index.html) is the framework used by the source models in this export path; its Python package is named `torch`. TorchScript and TorchDynamo are PyTorch mechanisms used by its [ONNX exporters](https://docs.pytorch.org/docs/stable/onnx.html), not separate command-line tools you need to run.

The CLI's default path uses the TorchScript exporter. `--dynamo` selects the TorchDynamo exporter, which can translate model operations differently and produce different metadata. Start with the default unless your model's guidance requires another exporter. An exporter may not be able to produce the requested operator set, or [opset](model-graphs.md#opsets-and-versioning); review warnings and the resulting graph.

Use `--export-config` for export parameters such as `opset_version`. Keep a separate baseline for each exporter or shape change, and validate it on the target EP.

### Input shapes and specifications

- `--shape-config` supplies model-specific shape overrides, such as image height and width or sequence length.
- `--input-specs` supplies explicit named tensor specifications.
- `--dynamic-axes` supplies symbolic dimension mappings where supported.
- `--batch-size` controls the static example batch size.

Dynamic shapes aren't interchangeable with static shapes on all EPs. Ensure that input names, dtypes, and dimensions match the model, and that the target supports the exported form.

### Composite models

Some models export cooperating graphs rather than one file. Without `--submodel`, export processes the supported components. To select one component:

```powershell
winml export -m google-t5/t5-small --task translation -o .\t5.onnx --submodel encoder
```

The selected component still receives a component-specific filename, such as `t5_encoder.onnx`; it isn't written to the literal `t5.onnx` path. Use the paths reported by the command.

## Hierarchy tagging

By default, export preserves source module information in ONNX node metadata. This helps relate graph operators to the source architecture.

| Metadata | Purpose |
|---|---|
| `winml.hierarchy.tag` | Source module path associated with a node. |
| `winml.hierarchy.depth` | Depth in the recovered module hierarchy. |
| `winml.io.inputs`, `winml.io.outputs` | Model input/output specifications. |

TorchScript and TorchDynamo recover hierarchy differently. A fallback root tag doesn't establish an exact node-to-module mapping. Use metadata as an inspection aid, not as proof of complete tracing fidelity.

Export can also produce hierarchy metadata sidecars. `--with-report` requests additional reports. Use `--no-hierarchy` when you don't need these tags or need to interoperate with a tool that doesn't preserve them.

The `--torch-module` option is currently reserved and logs a warning rather than implementing module-targeted export. Use supported component selection or the model-specific workflow instead.

## Where it goes wrong

**Task mismatch:** Select the task that matches the checkpoint and output head. Loading a base checkpoint with a new classification head doesn't produce a trained classifier.

**Shape or dtype mismatch:** Inspect the input specification and provide the required dimensions and types. Avoid changing the graph merely to silence an error without checking model behavior.

**Provider limitations:** Inspect the exported opset and operator decompositions, analyze the intended target, and test full inference. Switching exporters can change compatibility.

**Existing output:** Use a new output path or explicitly add `--overwrite`. Keep any external weights together with the graph that references them.

## See also

- [Export command](../commands/export.md)
- [Inspect command](../commands/inspect.md)
- [Analyze and optimize](analysis-optimization.md)
- [CLIP composite sample](../samples/clip-composite.md)
