---
title: "Windows ML CLI"
description: Prepare AI models for Windows ML with tools to export, analyze, optimize, quantize, compile, benchmark, and evaluate models.
author: GrantMeStrength
ms.author: jken
ms.topic: overview
ms.date: 11/03/2026
---

# Windows ML CLI

Windows ML CLI is a command-line tool for preparing AI models for [Windows ML](overview.md). Start with a supported pretrained model, convert it to ONNX if needed, and prepare it for your target hardware. Then measure how quickly it runs and evaluate its accuracy before integrating it into your Windows app.

This guide is for Windows app developers who want to use an existing model rather than train one. Familiarity with PowerShell and installing Python packages is helpful; you don't need to understand PyTorch export internals to complete the CPU quickstart.

The CLI provides a common workflow for CPUs, GPUs, and neural processing units (NPUs), including hardware from AMD, Intel, NVIDIA, and Qualcomm. It uses execution providers (EPs), software components that run model operations on a particular hardware target. You can run individual steps interactively or save a build configuration for use in scripts and CI/CD pipelines.

Windows ML CLI prepares and tests models; your app uses Windows ML to run them. For an introduction to the runtime and app integration, see [Get started with Windows ML](get-started.md).

## Key concepts

| Term | What it means in this workflow |
|---|---|
| [Hugging Face Hub](https://huggingface.co/docs/hub/models-the-hub) | A service for sharing models and their supporting files. A model ID such as `microsoft/resnet-50` identifies a repository on the Hub. |
| [PyTorch](https://docs.pytorch.org/docs/stable/index.html) | A machine-learning framework used to define and train models. Its Python package is named `torch`. The CLI uses it when exporting supported source models. |
| [ONNX](command-line/concepts/model-graphs.md) | Open Neural Network Exchange, a format for representing models independently of the framework used to create them. Export converts a source model to this format. |
| [Graph](command-line/concepts/model-graphs.md) | The model's calculation steps, called operators, connected by tensors (arrays of values). Optimization changes this representation to suit a target runtime or device. |
| Inference | Running a trained model on input data to produce results, such as image classifications or generated text. Benchmarking measures the performance of this execution. |
| [Execution provider (EP)](command-line/concepts/execution-providers-devices.md) | The runtime component that implements model operations for a hardware target. A GPU or NPU is the device; an EP is the software that enables the runtime to use it. |
| [Precision](command-line/concepts/quantization.md) | The numerical format used for model values. For example, FP32 uses 32-bit floating-point numbers; lower-precision formats can reduce memory use but can affect accuracy. |
| [Quantization](command-line/concepts/quantization.md) | Representing model values with fewer bits, often as integers, to reduce model size or computation cost. Hardware support and accuracy must be checked after conversion. |

## What you can do

| Goal | Windows ML CLI capabilities |
|---|---|
| Find a starting point | Browse the model catalog and inspect supported tasks and model architectures. |
| Bring your own model | Export supported Hugging Face models to ONNX, or start from a local ONNX file or a pre-exported ONNX file on the Hugging Face Hub. |
| Understand compatibility | Analyze operator and subgraph support for a target EP, identify unsupported or unknown patterns, and generate optimization recommendations. |
| Prepare for your target hardware | Apply graph transformations, quantize with calibration, and compile for EPs that support ahead-of-time compilation. |
| Measure the result | Benchmark latency and throughput, collect available hardware metrics, and profile operators on supported providers. |
| Check model quality | Evaluate on task datasets or compare model outputs against a reference after conversion or optimization. |
| Automate the workflow | Generate a reusable JSON build configuration, run the pipeline with `winml build`, or use the provided skills with an AI agent. |

> [!IMPORTANT]
> Model support, precision options, compilation support, and profiling capabilities vary by model and execution provider. A successful export or static analysis does not establish runtime compatibility, accuracy, or performance on every device. Validate the model on your intended deployment hardware.

### Models and tasks

Windows ML CLI supports workflows across vision, language, audio, and multimodal models. Examples include image classification, object detection, segmentation, depth estimation, text classification, embeddings, question answering, speech recognition, and text generation.

Support is specific to the model architecture, task, precision, and target EP. A recognized task doesn't mean every model for that task has a validated export or evaluation path. Use `winml inspect` to check a model and `winml catalog` to browse curated starting points. See [Supported models](command-line/reference/supported-models.md) for validated configurations and compatibility results.

Some models contain cooperating components rather than a single graph. For example, CLIP uses separate text and vision encoders, and supported decoder language models can use a GenAI bundle. See the [CLIP composite-model sample](command-line/samples/clip-composite.md) and [Qwen3 bundle sample](command-line/samples/generative-model-bundle.md).

### Command reference

Run the pipeline with `winml build`, or use individual commands when you need to inspect or customize a stage.

| Stage | Commands |
|---|---|
| Discover hardware and models | [`sys`](command-line/commands/sys.md), [`inspect`](command-line/commands/inspect.md), [`catalog`](command-line/commands/catalog.md) |
| Configure a workflow | [`config`](command-line/commands/config.md) |
| Prepare a model | [`export`](command-line/commands/export.md), [`analyze`](command-line/commands/analyze.md), [`optimize`](command-line/commands/optimize.md), [`quantize`](command-line/commands/quantize.md), [`compile`](command-line/commands/compile.md) |
| Execute the configured pipeline | [`build`](command-line/commands/build.md) |
| Measure performance and quality | [`perf`](command-line/commands/perf.md), [`eval`](command-line/commands/eval.md) |

## Prerequisites

- A Windows PC. For NPU workflows, use Windows 11, version 24H2 or later, with a compatible NPU, driver, and execution provider.
- **Python 3.11, x64 (AMD64)**. Native Arm64 Python isn't supported by the CLI's current dependency set.
- The [uv package manager](https://docs.astral.sh/uv/getting-started/installation/).
- An internet connection to install packages and download models, execution providers, or datasets when required. Some Hugging Face resources require authentication or acceptance of their license terms.

You don't need an NPU to get started. The quickstart below uses CPU inference so you can establish a baseline before targeting other hardware.

> [!NOTE]
> On Windows on Arm, use the x64 build of Python 3.11 under emulation, rather than native Arm64 Python. The installation commands below explicitly select x64 Python on both x64 and Arm64 PCs.

## Install Windows ML CLI

Run these commands in PowerShell from a directory where you want to create a Python environment:

```powershell
uv python install cpython-3.11-windows-x86_64-none
uv venv --python cpython-3.11-windows-x86_64-none
uv pip install winml-cli
```

Use `uv run` to invoke the CLI without activating the environment:

```powershell
uv run winml --version
uv run winml sys --list-device --list-ep
```

The `sys` command lists the compute devices and execution providers available on your machine. If an expected device or EP is missing, check your driver and provider requirements before building for that target. See the [sys command reference](command-line/commands/sys.md).

## Quickstart: Build and benchmark a model

This example uses ResNet-50, a pretrained model for classifying images. It exports and optimizes the model, then measures how quickly the resulting ONNX model runs on the CPU. It keeps 32-bit floating-point (FP32) values and skips quantization and compilation, giving you an initial performance measurement to compare with later changes.

### 1. Inspect model support

```powershell
uv run winml inspect -m microsoft/resnet-50
```

Review the detected task, model class, and support status before starting the build. To browse other starting points, use `uv run winml catalog` or see [Supported models](command-line/reference/supported-models.md).

### 2. Build the model

```powershell
uv run winml build -m microsoft/resnet-50 -o .\resnet_out --device cpu --ep cpu --precision fp32 --no-quant --no-compile
```

The CLI generates a build configuration, exports the model to ONNX, and runs the enabled analysis and optimization steps. In this example, `--no-quant` skips quantization and `--no-compile` skips EP compilation.

For this single-model ONNX workflow, the output directory contains:

- **`model.onnx`**: The final model from the enabled build stages.
- **`winml_build_config.json`**: The build configuration, which you can inspect, customize, and reuse.
- **Intermediate model files**: Outputs from completed stages, such as export and optimization.
- **Analysis output**: Compatibility findings and optimization recommendations when analysis runs.

Keep any external weight files with the ONNX files that reference them. For details about intermediate files and deployment artifacts, see [Output layout](command-line/reference/output-layout.md).

### 3. Measure performance

```powershell
uv run winml perf -m .\resnet_out\model.onnx --device cpu --ep cpu --iterations 50 --monitor -o .\resnet_out\perf_cpu.json
```

The benchmark runs warm-up iterations before collecting timing samples. The JSON report includes latency statistics and throughput, plus available hardware metrics when monitoring is enabled.

By default, this ONNX benchmark uses generated inputs. It measures model execution, not the complete application pipeline or task accuracy. Use representative input tensors with `--input-data` when appropriate, and evaluate accuracy separately.

## Target a GPU or NPU

Use `--device` to select a hardware category and `--ep` to select an execution provider. For example, on a machine with a compatible DirectX 12 GPU, benchmark the floating-point model with DirectML:

```powershell
uv run winml perf -m .\resnet_out\model.onnx --device gpu --ep dml --iterations 50 -o .\resnet_out\perf_gpu.json
```

Common provider aliases include:

| Hardware target | EP alias |
|---|---|
| CPU | `cpu` |
| DirectX 12 GPU through DirectML | `dml` |
| Qualcomm NPU | `qnn` |
| AMD NPU | `vitisai` |
| Intel CPU, GPU, or NPU through OpenVINO | `openvino` |
| NVIDIA RTX GPU through TensorRT RTX | `nv_tensorrt_rtx` |
| Supported AMD GPU through MIGraphX | `migraphx` |

Provider availability depends on the device, driver, and installed runtime packages. Use `winml sys` to check your machine and see [Execution providers and devices](command-line/concepts/execution-providers-devices.md) for the current compatibility details.

For commands that support automatic device selection, `--device auto` prioritizes an available NPU, then GPU, then CPU. This is a selection policy, not a benchmark-based guarantee that the selected device is fastest for your model.

NPU workflows can require a different precision or graph layout from a CPU baseline. Follow the [Hugging Face model to NPU tutorial](command-line/tutorials/prepare-image-classification-model.md) for a target-specific workflow.

> [!IMPORTANT]
> A portable ONNX graph and a compiled EP-specific artifact have different deployment constraints. Don't assume a compiled model can run with another provider or on every device. Validate the target EP's compatibility requirements and retain any external weights or EP context files required by the model.

## Bring an existing ONNX model

You can use Windows ML CLI without exporting from Hugging Face. Start by analyzing a local model for your intended EP. The following example uses Qualcomm QNN as the analysis target:

```powershell
uv run winml analyze -m .\my_model.onnx --device npu --ep qnn --optim-config .\optim_config.json
```

The analyzer reports supported, partially supported, unsupported, and unknown operators or patterns. It also writes recommended graph optimization settings to the specified JSON file. Treat unknown results as a need for further investigation, not as confirmation of support.

Apply the recommendations, then analyze the optimized result:

```powershell
uv run winml optimize -m .\my_model.onnx -c .\optim_config.json --device npu --ep qnn -o .\my_model_optimized.onnx
uv run winml analyze -m .\my_model_optimized.onnx --device npu --ep qnn
```

You can also pass an existing ONNX file to `winml build`; the pipeline skips export. For the complete workflow, including quantization and compilation, see [Bring your own ONNX model](command-line/tutorials/build-existing-model.md).

## Customize model export

Use `winml export` to export a supported Hugging Face model or a compatible local checkpoint directory independently of the build pipeline:

```powershell
uv run winml export -m microsoft/resnet-50 -o .\resnet_export.onnx --batch-size 1 --with-report
```

The exporter can preserve PyTorch module hierarchy as ONNX node metadata, helping you relate graph operators to their originating modules. Here, a module is a component of the original PyTorch model. With `--with-report`, the exporter also generates export reports for inspection. See [Load and export](command-line/concepts/model-loading-export.md) for an introduction to the PyTorch export mechanisms and their options.

For models with specific input requirements, you can configure:

- **Input shapes** with `--shape-config`, such as sequence length or image dimensions.
- **Input tensor specifications** with `--input-specs`.
- **Dynamic dimensions** with `--dynamic-axes`, when the model and target provider support them.
- **ONNX export settings** with `--export-config`, including the requested opset.
- **Exporter selection** with `--dynamo` to opt into the TorchDynamo exporter rather than the default TorchScript path.

These options aren't interchangeable across every workflow. Provider requirements, static-shape constraints, and exporter operator decompositions can affect whether the result compiles or runs. See [Load and export](command-line/concepts/model-loading-export.md) and the [export command reference](command-line/commands/export.md).

> [!CAUTION]
> Some models require custom Python code. Only enable `--trust-remote-code` for model repositories you trust; this option allows code from the model repository to execute locally.

## Quantize and calibrate a model

Quantization reduces the precision used for weights and activations. Depending on the model and target EP, it can reduce memory requirements or enable more efficient execution, but it can also change accuracy.

The build workflow supports precision selection such as `fp32`, `fp16`, `int8`, `int16`, and mixed weight/activation specifications, subject to the selected recipe and provider. The standalone `winml quantize` command provides four types of conversion:

| Mode | Calibration | Intended use |
|---|---|---|
| `fp16` | Not required | Reduce floating-point precision on compatible targets. |
| `int4` | Not required | Apply RTN weight-only quantization to supported operators. |
| `dynamic` | Not required | Quantize weights and compute activation scales at runtime. |
| `int8`, `int16`, `w{x}a{y}` | Required | Apply static QDQ with types supported by the target EP. |

Repeat `--precision` to chain supported conversions, such as `--precision int4 --precision fp16`. Build precision defaults and standalone conversion modes aren't interchangeable.

For example, quantize the optimized ResNet-50 model with task-based calibration:

```powershell
uv run winml quantize -m .\resnet_out\model.onnx -o .\resnet_out\resnet_int8.onnx --precision int8 --task image-classification --model-id microsoft/resnet-50 --samples 32 --method minmax
```

For a supported task, `--task` and `--model-id` select the default calibration dataset and matching model preprocessing. Access to that dataset might require authentication or acceptance of its terms.

Quantization controls include:

- Calibration range estimation with `minmax`, `entropy`, or `percentile`.
- Separate weight and activation types, including mixed precision such as `w8a16`.
- Per-channel weight quantization and symmetric quantization.
- Calibration sample count.

When task-based calibration isn't available or no task is supplied, the quantizer can use generated calibration inputs. These are useful for exercising the pipeline, but shouldn't be treated as evidence of production accuracy. Check that calibration data represents your workload and evaluate the quantized model against its floating-point baseline.

See the [quantize command reference](command-line/commands/quantize.md) for supported types, option precedence, and calibration behavior.

## Compile and package for deployment

Use `winml compile` when your target EP supports preparing a model ahead of inference. For EPs with ahead-of-time compilation, the result can include an **EP context** containing the compiled execution plan, reducing work during model initialization.

For example, on a compatible Qualcomm NPU system, compile the quantized model from the preceding section:

```powershell
uv run winml compile -m .\resnet_out\resnet_int8.onnx --device npu --ep qnn -o .\resnet_out\resnet_qnn.onnx
```

Compilation consumes the model you supply; it doesn't perform a separate quantization pass. Ensure the graph and precision meet the provider's requirements first.

For QNN, the default layout stores the compiled EP context in an external sidecar file. Use `--embed` to embed the context in the ONNX file instead. Embedding the context doesn't remove any separate external-weight requirements. Package all files referenced by the model and preserve their relative locations.

Compilation validates the result with a trial inference by default. That check requires compatible target hardware and doesn't replace task-level accuracy evaluation. You can also forward provider-specific settings with `--ep-options`.

Treat compiled artifacts as target-specific outputs. Their compatibility can depend on the provider, hardware, runtime, and driver versions; don't assume that one compiled artifact covers every deployment device.

See [Compile and EPContext](command-line/concepts/compilation-execution-provider-context.md), the [compile command reference](command-line/commands/compile.md), and [Distribute models for Windows ML](model-distribution.md).

## Diagnose performance and resource use

Beyond the quickstart benchmark, `winml perf` provides controls for investigating model performance:

| Capability | How to use it |
|---|---|
| Control timing and inputs | Set warm-up and measurement iterations, use `--duration` for a minimum measurement period, or provide input tensors through `--input-data`. Duration mode isn't available with operator tracing. |
| Monitor resource use | Add `--monitor` to collect available CPU, RAM, accelerator utilization, and device-memory metrics. |
| Inspect model-loading memory | Review the `load_memory` section of supported benchmark reports separately from steady-state inference measurements. |
| Select a physical adapter | Use `--device-luid` with an adapter LUID reported by `winml sys`, for supported EP/device pairs on the `winml-ort` runtime. |
| Profile operators | Use `--op-tracing basic` or `--op-tracing detail` where supported. Trace availability and compilation requirements differ by provider. |
| Investigate individual modules | Use `--module` for supported Hugging Face model workflows to build and benchmark matching PyTorch module types. |
| Control model reuse | Use `--use-cache`, `--no-use-cache`, or `--rebuild` for workflows that build models before benchmarking. |

Report fields and monitoring support vary by runtime, provider, and driver. An unavailable memory counter isn't equivalent to zero memory use. Keep the device, input shapes, runtime, precision, cache state, and monitoring settings consistent when comparing results.

For supported ONNX Runtime GenAI bundles, `--runtime ort-genai` measures generation-specific metrics, including **time to first token** and **decode tokens per second**. These aren't interchangeable with single-inference ONNX latency and throughput. Record the prompt, generation length, and runtime settings when comparing language-model results.

See the [perf command reference](command-line/commands/perf.md) for report fields and feature limitations.

## Evaluate accuracy before deployment

Performance improvements must be considered alongside model quality. Use `winml eval` to:

- Evaluate a supported task on a dataset, with the appropriate model preprocessing and task metrics.
- Compare an ONNX candidate with a reference model using the same inputs.
- Evaluate a supported Hugging Face checkpoint directly with `--runtime pytorch` to establish a source-model baseline.

For example, compare the optimized ResNet-50 output with its exported ONNX baseline:

```powershell
uv run winml eval -m .\resnet_out\model.onnx --mode compare --reference .\resnet_out\export.onnx --device cpu --ep cpu --reference-device cpu --reference-ep cpu -o .\resnet_out\compare.json
```

Compare mode reports output similarity and uses generated inputs unless you provide input data. It isn't a substitute for task-level accuracy evaluation on representative data. For quantized models, use representative calibration data and measure accuracy against your floating-point baseline.

See the [eval command reference](command-line/commands/eval.md) for dataset configuration, input requirements, and reference-model options.

## Save and automate a build configuration

Use `winml config` when you want to review or customize the pipeline before running it:

```powershell
uv run winml config -m microsoft/resnet-50 --device cpu --ep cpu --precision fp32 --no-quant --no-compile -o .\resnet_cpu.json
uv run winml build -c .\resnet_cpu.json -m microsoft/resnet-50 -o .\resnet_ci --no-quant --no-compile
```

Check the configuration into source control and reuse it in your build pipeline. The configuration stores loader and export settings, not the source checkpoint path, so pass the model identifier with `-m` when running the build. For repeatable comparisons, also control package versions, model and dataset revisions, calibration inputs, and the target runtime and hardware environment. A saved configuration alone doesn't guarantee identical results across different environments.

For the configuration format and stage controls, see [Config and build](command-line/concepts/configuration-build.md).

You can also invoke supported model preparation and evaluation workflows from Python. See the [Python API reference](command-line/reference/python-api.md) for configuration types and programmatic entry points.

## More workflows

- **Build language-model bundles.** For supported model families and targets, `winml build --export-type optimized` creates an ONNX Runtime GenAI bundle instead of a single ONNX model. See the [Qwen3 bundle sample](command-line/samples/generative-model-bundle.md). Supported targets are recipe-specific.
- **Work with an AI agent.** The repository provides skills for model preparation, adding model support, and experimental optimization investigations. Load the appropriate skill into an agent host that supports skills or custom instructions. See [Use Windows ML CLI with AI agents](command-line/getting-started/agent-skill/index.md).
- **Use a graphical interface.** The Foundry Toolkit extension for Visual Studio Code provides a Model Conversion workflow that invokes Windows ML CLI. See the [UI quickstart](command-line/getting-started/ui-quickstart.md) for setup and the integration's current release status.

### Preview and experimental workflows

Some repository workflows have additional release and dependency constraints:

- **CGIR/CGC workflows** use preview runtime or execution-provider packages. CGC adapter and pattern inspection also requires a compatible D3D12 runtime and driver for the relevant queries. Don't assume these prerequisites are included with the standard CLI installation.
- **GGUF benchmarking** through the Windows ML Tasks llama.cpp path is experimental. See the [GGUF sample](command-line/samples/language-model-benchmark.md) for requirements and report differences.

These workflows have their own status and requirements; CLI availability doesn't imply that every backend or integration is generally available.

## Privacy and feedback

Windows ML CLI collects optional usage telemetry with consent. On the first interactive run, the CLI prompts for your choice; pressing Enter accepts and entering `n` declines. You can change the choice later through `telemetry.consent` in `%USERPROFILE%\.winml\config.json`. Telemetry is disabled in non-interactive and CI/CD contexts. See the [Windows ML CLI privacy statement](command-line/privacy.md) for details.

For CLI issues and feature requests, use the [Windows ML CLI GitHub repository](https://github.com/microsoft/winml-cli/issues).

## Related content

- [Windows ML overview](overview.md)
- [Get started with Windows ML](get-started.md)
- [Install Windows ML CLI](command-line/getting-started/installation.md)
- [Windows ML CLI command reference](command-line/commands/overview.md)
- [Windows ML CLI troubleshooting](command-line/troubleshooting.md)
