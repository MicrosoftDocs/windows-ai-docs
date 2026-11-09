---
title: "Eval and datasets with Windows ML CLI"
description: Choose dataset evaluation or reference comparison in Windows ML CLI, configure preprocessing, and interpret model quality measurements.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Eval and datasets

`winml eval` measures task quality or output similarity rather than latency. Use dataset
evaluation to compare predictions with labels, or `--mode compare` to compare a
candidate with a reference model. Save a JSON report with `--output`.

## Choose an evaluation mode

| Goal | Workflow |
|---|---|
| Measure task accuracy after export or quantization | Evaluate representative labeled data with the correct task and preprocessor. |
| Detect numerical differences between ONNX graphs | Use `--mode compare --reference` with matching model inputs. |
| Evaluate the original checkpoint without export | Use `--runtime pytorch` with a supported CPU or CUDA-backed GPU target. |

Compare mode doesn't require labels. Random inputs provide an initial check; use
representative inputs for meaningful numerical comparisons. Similar outputs don't prove
acceptable task accuracy, and a small validation sample doesn't establish production coverage.

For compare-mode `--input-data`, NumPy archive keys match model input names and the leading
axis is the sample axis. A static batch size groups those rows into batches. In contrast,
`perf --input-data` uses the entire archive as one batch.

## Dataset evaluation

The dataset is the source of truth. Eval iterates over dataset rows, runs each sample
through the model, and compares the prediction to the label recorded in the dataset. This
means the dataset must have both input features and ground-truth labels, and the columns
carrying those values must be wired to the model's inputs and outputs. Windows ML CLI handles
standard tasks automatically, but the column-mapping flags let you override the defaults
for non-standard datasets.

## What eval reports

The metric reported depends on the task. Classification tasks produce accuracy (top-1 and
optionally top-5). Object detection tasks produce mean average precision (mAP). The exact
set of metrics is printed to stdout and saved to the file specified by `--output`. The
`--output` flag accepts any `.json` path; if omitted, results are printed but not persisted.
Use `--schema` to print the expected dataset schema for a given task without running eval,
which is useful when you are preparing a custom dataset.

## Picking a dataset

`--dataset` takes a Hugging Face dataset path — for example `imagenet-1k` or `glue`. If
you omit it, Windows ML CLI selects a default dataset based on the detected task. For datasets
that have multiple configurations, `--dataset-name` picks the specific config (e.g.
`--dataset-name mrpc` when using the `glue` dataset).

By default eval runs on the `validation` split; `--split` overrides this. Full validation
sets can be large. During development, `--samples 200` caps the run to 200 rows so you get
quick feedback. For very large datasets that you prefer not to download fully, `--streaming`
fetches rows on demand instead of materialising the whole dataset locally. `--shuffle`
(on by default) randomises sampling order so a capped run is representative rather than
biased toward the first rows.

## Column mapping

Windows ML CLI must know which dataset column feeds which model input and which column holds
the ground-truth label. For well-known task/dataset combinations this mapping is built in.
When it is not, use `--column key=value` to declare it. The `key` is the name the task
pipeline expects (e.g. `input_column`) and `value` is the actual column name in the
dataset (e.g. `image`). You can repeat `--column` as many times as needed.

When the integer label IDs in the dataset do not match the class indices the model was
trained against, `--label-mapping` accepts a JSON file of the form `{"class_name": id}`
that translates between the two spaces. This is common with models fine-tuned on a
relabelled subset of a public dataset.

## Why eval after quantization

Quantization is a lossy transformation. Converting weights from float32 to int8, or
activations to a narrow range, introduces rounding error that accumulates differently
across architectures and calibration data. The impact on accuracy cannot be predicted
analytically; it must be measured. Running `winml eval` before and after quantization
gives you a concrete accuracy delta. A drop within your acceptable threshold confirms the
quantized model is ready; a larger drop means you should revisit calibration settings or
switch to a less aggressive quantization scheme.

Make this a habit: quantize, then eval. Comparing two `--output` JSON files is a reliable,
reproducible record that the trade-off between performance and accuracy was explicitly
checked. See [Quantization](quantization.md) for the full quantization workflow.

## See also

- [Quantization](quantization.md) — calibrate and quantize a model, then verify with eval
- [Perf and monitoring](performance-monitoring.md) — measure latency and throughput after accuracy is confirmed
- [`winml eval` command reference](../commands/eval.md) — all flags with examples
