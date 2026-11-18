---
title: Generate text with your own language model using Windows ML
description: Use the experimental Windows ML Text Generation API to run a text-generation loop over a language model you bring yourself.
ms.date: 11/18/2026
ms.topic: concept-article
dev_langs:
- cpp
- csharp
- python
---

# Generate text with your own language model using Windows ML

> [!IMPORTANT]
> The Windows ML Runtime and Text Generation APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

> [!IMPORTANT]
> The Windows ML Runtime and Text Generation APIs are currently available in C++ and Python. C# is supported through a temporary wrapper that you copy into your project; a future update will include the C# APIs natively. See [Set up a project](./get-started.md#configure-your-project).

The Windows ML Text Generation API simplifies running text generation over your own language model. It handles tokenization, autoregressive decoding, streaming output, and cancellation for you, on top of the [Windows ML Runtime API](./overview.md) — similar to how [ONNX Runtime GenAI](https://github.com/microsoft/onnxruntime-genai) simplifies that loop on top of ONNX Runtime. Windows doesn't ship a model for this API; you choose, package, and distribute the model with your app. It's one of the [Windows ML Task APIs](../tasks/overview.md).

> [!TIP]
> If you don't need a specific model and just want on-device text generation with a model Microsoft manages, use one of the [ready-to-use local LLMs](/windows/ai/apis/local-llms) instead.

In Windows ML 2.9, you can specify a model for a Task, as [Choose how a Task runs your model](../tasks/choose-model-path.md) describes. This is the shortest path: you build no pipeline and pass no tokenizer. Alternatively, you create the underlying Runtime objects — the model, the execution target, and the pipeline — and pass them to the session, as the rest of this article shows. This path doesn't hide hardware placement; it only removes the need to write your own token loop.

## Supported models

When you specify a model for the Task, Windows ML compiles it for the GPU with the [CGC backend](./model-compilation-execution.md). Start with the [recommended models](./model-compilation-execution.md#recommended-models), such as Llama-3.2-1B-Instruct, Qwen 3.0 0.6B, Gemma 3 1B It, Phi-3.5-mini-instruct, Qwen2.5-0.5B-Instruct (the samples' model), and Qwen3.8-27B (NVIDIA GPUs only), as GGUF files. Other dense decoder models in unquantized safetensors or GGUF form might work, but they aren't validated yet. A model that Windows ML doesn't support fails with `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`.

With a pipeline that you build, the Text Generation API accepts:

- **ONNX** (`.onnx`) or **ORT format** (`.ort`) models, run through ONNX Runtime. The model must expose a token-ID input, a logits output, and one fixed-capacity state tensor pair that holds the key/value cache and sequence position. You supply a separate tokenizer source (for example, a `tokenizer.json`).
- **GGUF** (`.gguf`) models, run through the llama.cpp backend. The tokenizer is embedded in the model, so no separate tokenizer source is needed.

The samples validate this contract against the **Qwen2.5-0.5B-Instruct** model family, exported with a Task-specific graph that packs the key/value cache and sequence position into one state pair. Other decoder-only language models that expose the same token input, logits output, and state contract should also work.

## Generate text

A text-generation session wraps a tokenizer and a stateful pipeline (the model that owns the key/value cache). Load the model and tokenizer, describe the model's token input, logits output, and state contract, and build the session:

### [C#](#tab/csharp)

```csharp
// Temporary: WinMLRuntime and the other wrapper types in the WinMLSamples.Runtime
// namespaces aren't in the Windows ML package yet. Copy the wrapper files from
// https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp
// into your project. A future update will include the C# APIs natively.
using WinMLSamples.Runtime;
using WinMLSamples.Runtime.Tasks;

// Create the runtime and an execution target (CPU here; GPU/NPU also available).
using WinMLRuntime runtime = WinMLRuntime.Create();
using ExecutionTarget target = runtime.CreateCpuExecutionTarget();

// Load your ONNX model and its tokenizer.
using Model model = runtime.LoadModelFromFile(@"C:\models\qwen2.5-0.5b-instruct\task_model.onnx");
using Tokenizer tokenizer = Tokenizer.CreateFromFile(@"C:\models\qwen2.5-0.5b-instruct\tokenizer.json");

// Find the model's token input and logits output by name.
uint tokenInputIndex;
uint outputIndex;
using (OrtModelSchema schema = model.As<OrtModelSchema>())
{
    tokenInputIndex = schema.FindInputIndex("input_ids");
    outputIndex = schema.FindOutputIndex("logits");
}

// Describe the model's KV-cache state pair, and build the pipeline.
using PipelineBuilder builder = runtime.CreatePipelineBuilder();
using Stage stage = builder.AddModelStage(model, target, "my-text-generation");
using (StatefulStageOptions stateful = stage.As<StatefulStageOptions>())
{
    stateful.AddStateTensorPair(1, 1);
    stateful.SequenceCapacityHint = 128;
}

stage.RequestOutput(outputIndex);
using Pipeline pipeline = builder.Build();

// Configure the Task with the pipeline, the token input, the logits output,
// and the stage that owns the state.
using WinMLTasks tasks = WinMLTasks.Create(runtime);
using TextGenerationTask task = tasks.TextGeneration.CreateTextGenerationTask(tokenizer);
using TextGenerationOptions options = tasks.TextGeneration.CreateTextGenerationOptions(64);
using TextGenerationConfiguration configuration =
    tasks.TextGeneration.CreateTextGenerationConfiguration();
configuration.Mode = TextGenerationConfigurationMode.Unified;
configuration.SetPipeline(TextGenerationPipelinePhase.Unified, pipeline);
configuration.SetTokenInput(TextGenerationPipelinePhase.Unified, stage, tokenInputIndex);
configuration.SetOutput(
    TextGenerationPipelinePhase.Unified, stage, outputIndex, TextGenerationOutputKind.Logits);
configuration.SetStateOwnerStage(stage);
configuration.SetTokenTarget(target);
configuration.SetTokenizer(tokenizer);
configuration.Validate();

// A TextGenerator keeps the session on a thread of its own, because a session
// belongs to the thread that created it, and streams tokens as IAsyncEnumerable<T>.
await using TextGenerator generator = await TextGenerator.CreateAsync(tasks, task, configuration);
```

### [C++](#tab/cpp)

```cpp
// Create the runtime and an execution target (CPU here; GPU/NPU also available).
ComPtr<IWinMLRuntime> runtime;
THROW_IF_FAILED(WinMLCreateRuntime(IID_PPV_ARGS(runtime.GetAddressOf())));
ComPtr<IWinMLExecutionTarget> target;
THROW_IF_FAILED(runtime->CreateCpuExecutionTarget(target.GetAddressOf()));

// Load your ONNX model and its tokenizer.
ComPtr<IWinMLModel> model;
THROW_IF_FAILED(runtime->LoadModelFromFile(
    L"C:\\models\\qwen2.5-0.5b-instruct\\task_model.onnx",
    nullptr,
    model.GetAddressOf()));
ComPtr<IWinMLTokenizer> tokenizer;
THROW_IF_FAILED(WinMLCreateTokenizerFromFile(
    L"C:\\models\\qwen2.5-0.5b-instruct\\tokenizer.json",
    tokenizer.GetAddressOf()));

// Describe the model's token input, logits output, and KV-cache state pair.
winml::tasks::text_generation::onnx::StateTensorPair stateTensorPairs[] = {{1, 1}};
winml::tasks::text_generation::Options options;
options.values = winml::tasks::MakeTextGenerationOptions(/* maxNewTokens */ 64);

winml::tasks::text_generation::onnx::BuildTextGenerationArguments arguments;
arguments.runtime = runtime.Get();
arguments.model = model.Get();
arguments.target = target.Get();
arguments.tokenizer = tokenizer.Get();
arguments.tokenInputName = L"input_ids";
arguments.outputName = L"logits";
arguments.debugName = L"my-text-generation";
arguments.stateTensorPairs = stateTensorPairs;
arguments.sequenceCapacityHint = 128;
arguments.outputKind = WINML_TEXT_GENERATION_OUTPUT_KIND_LOGITS;
arguments.eosPolicy = WINML_TEXT_GENERATION_EOS_POLICY_TOKENIZER_DEFAULT;
arguments.options = &options;

winml::tasks::text_generation::TextGeneration textGeneration;
THROW_IF_FAILED(winml::tasks::text_generation::onnx::BuildTextGeneration(
    arguments, textGeneration));
```

---

Call `GenerateText` with a prompt to start a streaming operation, then pull fragments from the returned stream until it reports completion:

### [C#](#tab/csharp)

```csharp
// Cancelling the token stops generation in the Runtime.
CancellationTokenSource cancellation = new();

// Read generated text one fragment at a time until the stream completes.
await foreach (TextGenerationUpdate update in
    generator.GenerateTextAsync("The sky is often", options, cancellation.Token))
{
    Console.Write(update.Text);
}

// The result is the authoritative text, token counts, and finish reason.
TextGenerationResult result = generator.LastResult!;
Console.WriteLine($"\nTokens: prompt={result.PromptTokenCount} generated={result.GetTokenIds().Length}");
Console.WriteLine($"Finished because: {result.FinishReason}");

// Closing the session reports a failure that disposing it would not.
await generator.CloseAsync();
```

### [C++](#tab/cpp)

```cpp
ComPtr<IWinMLCancellationSource> cancellation;
THROW_IF_FAILED(textGeneration.tasks->CreateCancellationSource(
    cancellation.GetAddressOf()));

ComPtr<IWinMLTextGenerationPullStream> stream;
THROW_IF_FAILED(textGeneration.session->GenerateText(
    L"The sky is often",
    textGeneration.options.Get(),
    cancellation.Get(),
    stream.GetAddressOf()));

// Pull generated text one fragment at a time until the stream completes.
WINML_TEXT_GENERATION_READ_STATUS status{};
UINT32 tokenId = 0;
LPCWSTR fragment = nullptr;
while (SUCCEEDED(stream->ReadNext(&status, &tokenId, &fragment)) &&
    status == WINML_TEXT_GENERATION_READ_STATUS_UPDATE)
{
    std::wprintf(L"%ls", fragment);
}

// The terminal result is the authoritative text, token counts, and finish reason.
ComPtr<IWinMLTextGenerationResult> result;
THROW_IF_FAILED(stream->GetResult(result.GetAddressOf()));
```

---

The completed result reports:

- The full generated text and token IDs
- Prompt and generated-token counts
- A finish reason — an end-of-sequence token, a stop token, the requested token limit, sequence capacity, cancellation, or an error

## Speculative decoding

Speculative decoding is off by default. A draft source proposes several tokens, the model verifies them in one step, and the Task keeps the tokens the model would have chosen itself. With greedy decoding, the text stays the same, apart from a rare near tie between two tokens. Select a method in the speculative options before you validate the configuration: `PROMPT_LOOKUP`, `MODEL`, or `DRAFT_MODEL`. Which methods a model supports depends on how you loaded the model; see [Speculative decoding](../tasks/choose-model-path.md#speculative-decoding). The terminal result reports the effective method and the drafted and accepted token counts.

## C#, C++, and Python

The native API is exposed through `WinMLTasks.h` and the typed C++ helpers under `winml/tasks/text_generation/TextGeneration.hpp`. Python applications use the `windowsml.tasks` projection together with `windowsml.runtime`. C# applications use CsWin32 bindings through a wrapper that you copy into your project for now; see [Set up a project](./get-started.md#configure-your-project).

The [Windows ML samples](../samples.md) include C++, C#, and Python text-generation samples. The samples show the full Runtime setup beside the text-generation configuration so you can see where the abstraction begins and how to customize the underlying workflow.

## See also

- [Windows ML Task APIs overview](../tasks/overview.md)
- [Choose how a Task runs your model](../tasks/choose-model-path.md)
- [Chat completion](../tasks/chat-completion.md)
- [Ready-to-use local LLMs on Windows](/windows/ai/apis/local-llms) (Phi Silica and other Microsoft-managed models)
- [Windows ML Runtime API overview](./overview.md)
- [Windows ML Runtime API concepts](./concepts.md)
- [Recognize speech with your own Whisper model using Windows ML](./speech-recognition.md)
- [Windows ML samples](../samples.md)
- [ONNX Runtime GenAI](https://github.com/microsoft/onnxruntime-genai)
