---
title: Windows ML CLI build outputs and deployment files
description: Identify final models, intermediate artifacts, external weights, and EP context files produced by Windows ML CLI before deployment.
author: GrantMeStrength
ms.author: jken
ms.topic: reference
ms.date: 10/29/2026
---

# Output layout

The output layout depends on the source model, enabled stages, and export type. Use the final paths reported by `winml build` rather than assuming every workflow writes the same filenames.

## Directory structure

For a single ONNX model built into an explicit output directory, the output commonly includes:

```text
output/
  model.onnx
  winml_build_config.json
  [intermediate ONNX files from completed stages]
  [external weight or EP context files, when required]
  [analysis reports and build metadata]
```

Hugging Face exports and existing ONNX inputs can use different intermediate filenames. Composite builds produce per-component outputs; optimized GenAI builds produce a bundle directory.

## File categories

### Final artifacts

| Artifact | What to retain |
|---|---|
| Final ONNX model | The path reported by the successful build. For a single-model explicit-output workflow, this is typically `model.onnx`. |
| External tensor data | Every external data file referenced by the ONNX graph, preserving relative paths. |
| EP context | External compiled context files referenced by an EPContext model, unless the context is embedded. |
| GenAI bundle | The cooperating ONNX graphs, configuration, tokenizer, and referenced weights/context required by the bundle. |

Don't assume that a final model is independent of all neighboring files. Copying only the `.onnx` file can leave it unable to load.

### Build records and intermediates

`winml_build_config.json` records the resolved pipeline settings. Analysis reports describe compatibility findings and optimization recommendations when those stages run. Build manifests and export metadata provide additional information where supported.

Intermediate graphs let you compare export, optimization, and quantization stages without repeating the full workflow. Retain them during validation. Before deleting an intermediate or sidecar, confirm that no final artifact references it.

## What gets written at each stage

Standalone commands such as `export`, `optimize`, and `quantize` write to the selected output path. `compile` can produce multiple related files. Use explicit names when chaining commands:

```powershell
winml optimize -m .\model.onnx --device cpu --ep cpu -o .\model_optimized.onnx
winml quantize -m .\model_optimized.onnx --precision fp16 -o .\model_fp16.onnx
```

These commands protect existing outputs by default. Use a new path or `--overwrite` when replacing one deliberately. For `build`, use `--rebuild` instead.

If you skip quantization but leave compilation enabled, the final artifact can still be compiled. The final model comes from the last enabled stage that completed, not necessarily the optimizer.

## External data

ONNX can store tensor data outside the graph file. The exporter, model size, and save settings affect this layout. Inspect the produced files and references rather than relying on a universal file-size threshold or assuming every sidecar is named `model.onnx.data`.

EP context embedding is separate from external tensor storage. `compile --embed` embeds the context; it doesn't guarantee that the model has no other file dependencies.

## Analyzer result

Use `winml analyze --output` to save a compatibility report. The report includes model metadata and target-specific findings, such as operator classifications and pattern counts.

Pattern counts can differ between EPs because their rule sets and deduplication differ. Compare the findings for the target you intend to deploy, and validate unknown or conditional support on that target.

## Cache and repeatable builds

`build --use-cache` writes to the CLI cache instead of an explicit `-o` directory. Those options are mutually exclusive. Cache-backed paths can include model-specific prefixes, so use the command's reported paths.

Save the configuration with the model revision, dependency versions, target EP, and calibration inputs for repeatable comparisons. Configuration reuse alone doesn't guarantee byte-identical results.

## See also

- [Build command](../commands/build.md)
- [Compile and EPContext](../concepts/compilation-execution-provider-context.md)
- [Qwen3 bundle sample](../samples/generative-model-bundle.md)
- [Build configuration](index.md)
