---
title: Generate text with your own language model using Windows ML
description: Use the experimental Windows ML Text Generation API to run a text-generation loop over a language model you bring yourself.
ms.date: 09/28/2026
ms.topic: concept-article
dev_langs:
- cpp
- python
---

# Generate text with your own language model using Windows ML

> [!IMPORTANT]
> The Windows ML Runtime and Text Generation APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

> [!IMPORTANT]
> The Windows ML Runtime and Text Generation APIs are currently available in C++ and Python; **C# is currently not supported.** Let us know if you're interested in using these APIs from C# by [posting an issue in the Windows ML GitHub repo](https://github.com/microsoft/WindowsML/issues)!

The Windows ML Text Generation API simplifies running text generation over your own language model. It handles tokenization, autoregressive decoding, streaming output, and cancellation for you, on top of the [Windows ML Runtime API](./overview.md) — similar to how [ONNX Runtime GenAI](https://github.com/microsoft/onnxruntime-genai) simplifies that loop on top of ONNX Runtime. Windows doesn't ship a model for this API; you choose, package, and distribute the model — an ONNX or GGUF file, plus a tokenizer — with your app.

> [!TIP]
> If you don't need a specific model and just want on-device text generation with a model Microsoft manages, use one of the [ready-to-use local LLMs](/windows/ai/apis/local-llms) instead.

You still create the underlying Runtime objects — the model, the execution target, and the pipeline — and pass them to the session. The API doesn't hide hardware placement; it only removes the need to write your own token loop.

## Supported models

The Text Generation API accepts:

- **ONNX** (`.onnx`) or **ORT format** (`.ort`) models, run through ONNX Runtime. The model must expose a token-ID input, a logits output, and one fixed-capacity state tensor pair that holds the key/value cache and sequence position. You supply a separate tokenizer source (for example, a `tokenizer.json`).
- **GGUF** (`.gguf`) models, run through the llama.cpp backend. The tokenizer is embedded in the model, so no separate tokenizer source is needed.

The samples validate this contract against the **Qwen2.5-0.5B-Instruct** model family, exported with a Task-specific graph that packs the key/value cache and sequence position into one state pair. Other decoder-only language models that expose the same token input, logits output, and state contract should also work.

## Generate text

A text-generation session wraps a tokenizer and a stateful pipeline (the model that owns the key/value cache). Load the model and tokenizer, describe the model's token input, logits output, and state contract, and build the session:

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

Call `GenerateText` with a prompt to start a streaming operation, then pull fragments from the returned stream until it reports completion:

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

The completed result reports:

- The full generated text and token IDs
- Prompt and generated-token counts
- A finish reason — an end-of-sequence token, a stop token, the requested token limit, sequence capacity, cancellation, or an error

## Known limitations

Today the session always uses greedy decoding: at each step it picks the highest-probability token. The following table shows what the Text Generation API supports today, independent of whether the underlying backend (ONNX Runtime or llama.cpp) is capable of more:

| Feature | Supported today |
|---|---|
| Greedy decoding | ✅ Yes |
| Sampling (top-p, top-k, temperature) | ❌ Not yet available |
| Speculative decoding | ❌ Not yet available |
| Multi-token prediction (MTP) | ❌ Not yet available |
| Custom logits processing | ❌ Not yet available |
| Chat templates | ❌ Not yet available |
| Structured output | ❌ Not yet available |

For GGUF models, the llama.cpp backend that Windows ML uses may support some of these techniques internally for specific model architectures, but the Text Generation API doesn't expose them yet. Check back in future releases for updated support.

## C++ and Python

The native API is exposed through `WinMLTasks.h` and the typed C++ helpers under `winml/tasks/text_generation/TextGeneration.hpp`. Python applications use the `windowsml.tasks` projection together with `windowsml.runtime`.

The [Windows ML samples](../samples.md) include a C++ and Python text-generation sample. The sample shows the full Runtime setup beside the text-generation configuration so you can see where the abstraction begins and how to customize the underlying workflow.

## See also

- [Ready-to-use local LLMs on Windows](/windows/ai/apis/local-llms) (Phi Silica and other Microsoft-managed models)
- [Windows ML Runtime API overview](./overview.md)
- [Windows ML Runtime API concepts](./concepts.md)
- [Recognize speech with your own Whisper model using Windows ML](./speech-recognition.md)
- [Windows ML samples](../samples.md)
- [ONNX Runtime GenAI](https://github.com/microsoft/onnxruntime-genai)
