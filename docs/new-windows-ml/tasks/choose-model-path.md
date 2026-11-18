---
title: Choose how a Task runs your model
description: Learn how loading published weights, an exported model, or a GGUF file for llama.cpp decides how an experimental Windows ML Task runs your model.
author: andrewleader
ms.author: aleader
ms.topic: concept-article
ms.date: 11/18/2026
dev_langs:
- cpp
- csharp
- python
---

# Choose how a Task runs your model

> [!IMPORTANT]
> Specifying a model for a Task is experimental, **not supported** for use in production environments, and requires Windows ML 2.9 experimental or later.

Instead of building a pipeline, you can specify a model for a Task. The call that loads the model decides which engine runs it. Windows ML never moves a model from one path to another, so a model that a path doesn't support fails instead of falling back.

## Choose a model source and execution path

In each row, your app passes the loaded model to the Task with `SetModel`, or with `SetComposition` for exported graphs. The load call decides which engine runs the model:

| Load call | Model | How the Task runs it |
|---|---|---|
| `LoadModelFromPath` | A checkpoint folder with `config.json`, such as a Hugging Face folder of safetensors weights | Windows ML compiles the model into programs for the [CGC backend](../runtime/model-compilation-execution.md) and runs it on the GPU target your app picked. |
| `LoadModelFromFile` or `LoadModelFromPath` | A GGUF file | The same as a checkpoint folder: the CGC backend runs it, not llama.cpp. A file whose architecture or weight types Windows ML doesn't support fails with `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`. |
| `IWinMLLlamaModelLoader::LoadModelFromFile` | A GGUF file | llama.cpp runs it, for text generation only. |
| `LoadModelFromFile` for each exported graph, then `SetComposition` | Exported `.onnx` graphs, CGC artifacts, or a GGUF decoder | Each graph runs on its pipeline backend, composed as your description says. |

> [!NOTE]
> This table is about passing a model to a Task. If you instead add a GGUF model to a pipeline that you build with `AddModelStage`, the `.gguf` extension selects llama.cpp. See [Windows ML Runtime API concepts](../runtime/concepts.md#backends).

> [!IMPORTANT]
> When Windows ML runs a model on the GPU with the [CGC backend](../runtime/model-compilation-execution.md), try the [recommended models](../runtime/model-compilation-execution.md#recommended-models) first. Other models might not load or might not run well yet.

Text generation from published weights supports dense decoder models as folders of unquantized safetensors weights or as GGUF files, but models outside the recommended list aren't validated yet. To run a GGUF file that Windows ML doesn't support, load it with the llama.cpp loader.

## Run published weights

Load the published weights, create a text generation configuration, and call `SetModel` with a GPU execution target. You pass no tokenizer: Windows ML creates the model's own tokenizer from its files, and you create the Task with that tokenizer.

### [C#](#tab/csharp)

```csharp
// Temporary: WinMLRuntime and the other wrapper types in the WinMLSamples.Runtime
// namespaces aren't in the Windows ML package yet. Copy the wrapper files from
// https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp
// into your project. A future update will include the C# APIs natively.
using WinMLSamples.Runtime;
using WinMLSamples.Runtime.Tasks;

using WinMLRuntime runtime = WinMLRuntime.Create();
using ExecutionTarget target = runtime.CreateExecutionTarget(
    ExecutionTargetKind.Gpu, ExecutionTargetPreference.Performance);

// A checkpoint folder loads with LoadModelFromPath; a single GGUF file
// loads with LoadModelFromFile.
using Model model = runtime.LoadModelFromPath(@"C:\models\qwen2.5-0.5b-instruct");

using WinMLTasks tasks = WinMLTasks.Create(runtime);
using TextGenerationConfiguration configuration =
    tasks.TextGeneration.CreateTextGenerationConfiguration();

// No SetTokenizer: SetModel creates the model's own tokenizer from its files.
using (ModelTextGenerationConfiguration modelConfiguration =
    configuration.As<ModelTextGenerationConfiguration>())
{
    modelConfiguration.SetModel(target, model, new TextGenerationModelOptions());
}

// The Task must use the configuration's tokenizer.
using Tokenizer tokenizer = configuration.GetTokenizer();
using TextGenerationTask task = tasks.TextGeneration.CreateTextGenerationTask(tokenizer);

// A TextGenerator keeps the session on a thread of its own and streams tokens.
await using TextGenerator generator = await TextGenerator.CreateAsync(tasks, task, configuration);

// Stream a reply from the generator.
```

### [C++](#tab/cpp)

```cpp
ComPtr<IWinMLRuntime> runtime;
THROW_IF_FAILED(WinMLCreateRuntime(IID_PPV_ARGS(runtime.GetAddressOf())));
ComPtr<IWinMLExecutionTarget> target;
THROW_IF_FAILED(runtime->CreateExecutionTarget(
    WINML_EXECUTION_TARGET_KIND_GPU,
    WINML_EXECUTION_TARGET_PREFERENCE_PERFORMANCE,
    target.GetAddressOf()));

// A checkpoint folder loads with LoadModelFromPath; a single GGUF file
// loads with LoadModelFromFile.
ComPtr<IWinMLModel> model;
THROW_IF_FAILED(runtime->LoadModelFromPath(
    L"C:\\models\\qwen2.5-0.5b-instruct", nullptr, model.GetAddressOf()));

ComPtr<IWinMLTasks> tasks;
THROW_IF_FAILED(WinMLCreateTasks(runtime.Get(), IID_PPV_ARGS(tasks.GetAddressOf())));
ComPtr<IWinMLTextGenerationTaskFactory> factory;
THROW_IF_FAILED(tasks.As(&factory));
ComPtr<IWinMLTextGenerationConfiguration> configuration;
THROW_IF_FAILED(factory->CreateTextGenerationConfiguration(configuration.GetAddressOf()));

// No SetTokenizer: SetModel creates the model's own tokenizer from its files.
ComPtr<IWinMLModelTextGenerationConfiguration> modelConfiguration;
THROW_IF_FAILED(configuration.As(&modelConfiguration));
// SetModel takes model options; zero-initialized options select the defaults.
WINML_TEXT_GENERATION_MODEL_OPTIONS modelOptions{};
THROW_IF_FAILED(modelConfiguration->SetModel(target.Get(), model.Get(), &modelOptions));

// The Task must use the configuration's tokenizer.
ComPtr<IWinMLTokenizer> tokenizer;
THROW_IF_FAILED(configuration->GetTokenizer(tokenizer.GetAddressOf()));
ComPtr<IWinMLTextGenerationTask> task;
THROW_IF_FAILED(factory->CreateTextGenerationTask(
    tokenizer.Get(), WINML_TEXT_GENERATION_EOS_POLICY_TOKENIZER_DEFAULT, 0, nullptr,
    task.GetAddressOf()));
ComPtr<IWinMLTextGenerationSession> session;
THROW_IF_FAILED(task->CreateSession(configuration.Get(), session.GetAddressOf()));

// Stream a reply from the session.
```

### [Python](#tab/python)

```python
from windowsml.runtime import ExecutionTargetKind, Runtime
from windowsml.tasks import Tasks

with (
    Runtime() as runtime,
    runtime.create_target(ExecutionTargetKind.GPU) as target,
    runtime.load_model(r"C:\models\qwen2.5-0.5b-instruct") as model,
    Tasks(runtime) as tasks,
    tasks.create_text_generation_configuration() as configuration,
):
    # No tokenizer: set_model creates the model's own from its files.
    configuration.set_model(target, model)

    # The task must use the configuration's tokenizer.
    tokenizer = configuration.tokenizer
    with (
        tasks.create_text_generation_task(tokenizer) as task,
        task.create_session(configuration) as session,
    ):
        ...  # Stream a reply from the session.
```

---

After binding, every pattern produces the same text generation session, with the same streaming, cancellation, token counts, and finish reasons. A chat completion Task can use it. In C++, you query the configuration for `IWinMLModelTextGenerationConfiguration` and call `SetModel`.

Call `SetLogicalCapacity` after `SetModel` to set the largest sequence, in tokens, that a session holds. A smaller capacity uses less GPU memory. Without it, a session holds the model's default sequence length.

### Cache compiled programs

The first session of a Task that uses the CGC backend compiles programs for the GPU, which can take many seconds. Set a program cache directory in the model options (`compiledProgramDirectory`, or `--program-cache` in the samples) to keep the compiled programs for later processes.

- By default, the cache keeps only the programs, a few megabytes for each model. A later session prepares the weights from the model's files again.
- `WINML_COMPILED_PROGRAM_STORAGE_PROGRAMS_AND_WEIGHTS` (`--cache-weights` in the samples) also keeps the prepared weights. A warm start is faster, but each program keeps its own copy, so the cache can take several times the model's size on disk.
- A model that can't reuse compiled programs, such as a Whisper checkpoint, refuses the directory with `ERROR_NOT_SUPPORTED`. The Task then compiles for each session without a cache.

## Run an exported model

When you exported your own graphs, load each one with `LoadModelFromFile`, set the tokenizer with `SetTokenizer`, and describe the graphs with `SetComposition`. You describe the inputs, outputs, and state, such as the key/value cache pair of a decoder, instead of wiring each stage. Each session composes the described models from Runtime stages, so you get a Task in a few lines.

## Run a GGUF file with llama.cpp

llama.cpp is an engine-specific choice. Windows ML uses it for a model only when your app loads the model with `IWinMLLlamaModelLoader` (`Runtime.load_llama_model` in Python), and only for text generation.

1. Query the runtime for `IWinMLLlamaModelLoader`. It's present when the Windows ML package includes the llama.cpp engine.
2. Load the model, and optionally a smaller draft model, with the loader's `LoadModelFromFile`.
3. Call `SetModel`, passing the draft model in the model options and selecting the `DRAFT_MODEL` speculative method. `SetModel` reads the tokenizer from the GGUF file.

A draft model must share the model's tokenizer and be loaded through the same loader. The llama.cpp payload must be deployed with your app: the `Microsoft.Windows.AI.MachineLearning.LibLlama.Core` NuGet package for C++, or the `windowsml-llama-core` wheel (`2.9.36a0`), which must match the `windowsml` wheel version, for Python. Without the payload, `SetModel` succeeds but the first `CreateSession` fails with `HRESULT_FROM_WIN32(ERROR_FILE_NOT_FOUND)`. For setup, see [Windows ML Runtime API concepts](../runtime/concepts.md#deploy-the-llamacpp-backend).

The llama.cpp sample runs on the CPU. A GPU target needs a llama.cpp GPU backend that you build and deploy yourself.

## Speculative decoding

Speculative decoding lets a faster draft source propose several tokens that the model verifies in one step. With greedy decoding, the text stays the same, apart from a rare near tie between two tokens. For text generation from published weights:

| Method | Draft model | Behavior |
|---|---|---|
| `NONE` | None | Sequential decoding. |
| `MODEL` | Optional | Drafts with a companion drafter that you load as the draft model, such as a DFlash 2 checkpoint. Without one, it uses the multi-token prediction layers that the model carries. |
| `PROMPT_LOOKUP` | None | Drafts tokens that followed an earlier occurrence of the end of the sequence. |
| `DRAFT_MODEL` | Required for llama.cpp | A separate, smaller model. Published weights don't support this method. |

Windows ML never looks for a draft model on its own. A configuration can report whether a method is supported before you create a session, and the terminal result reports the drafted and accepted token counts.

## Next steps

- [Windows ML Task APIs overview](./overview.md)
- [Generate text with your own language model](../runtime/text-generation.md)
- [Windows ML Runtime API concepts](../runtime/concepts.md)
