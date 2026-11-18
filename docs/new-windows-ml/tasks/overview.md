---
title: Windows ML Task APIs overview
description: Learn how the experimental Windows ML Task APIs run text generation, chat completion, speech recognition, and decisions over a model you bring.
author: andrewleader
ms.author: aleader
ms.topic: overview
ms.date: 11/18/2026
dev_langs:
- cpp
- csharp
- python
---

# Windows ML Task APIs overview

> [!IMPORTANT]
> The Windows ML Task APIs are experimental and **not supported** for use in production environments. Apps that try these APIs shouldn't be published to the Microsoft Store.

The Windows ML Task APIs enable common AI tasks, such as text generation, chat conversations, speech recognition, and answering typed questions, over a model that your app supplies. A Task handles tokenization, decoding, streaming, and cancellation, so you don't write the token loop yourself. Windows doesn't ship a model for these APIs: you choose, package, and distribute the model with your app.

The Task APIs build on the [Windows ML Runtime API](../runtime/overview.md). Windows ML 2.9 experimental lets you specify a model for a Task, instead of building a pipeline. For models, see [Find or train models](../models.md).

> [!TIP]
> If you want on-device AI models that Microsoft manages, see [ready-to-use AI models and APIs](../../overview.md#ready-to-use-ai-models-and-apis) instead.

## Available tasks

| Task | What it does | Article |
|---|---|---|
| Text generation | Runs the token loop over a language model with sampling, streaming, cancellation, and optional speculative decoding. | [Generate text](../runtime/text-generation.md) |
| Chat completion | Applies the model's chat template to a list of messages and streams the reply. | [Chat completion](./chat-completion.md) |
| Speech recognition | Transcribes audio from a speech model and streams the transcript. | [Recognize speech](../runtime/speech-recognition.md) |
| Decision | Answers a typed question about a text with a probability for each option, and generates no text. | [Make decisions](./decision.md) |

## Choose how your model runs

How your app loads the model decides how Windows ML runs it. Windows ML doesn't choose among engines for you and never falls back from one to another. For details, see [Choose how a Task runs your model](./choose-model-path.md).

| If you have | Your app | Windows ML |
|---|---|---|
| Published weights, as a checkpoint folder or a GGUF file | Loads the weights and calls `SetModel`. | Compiles the model into programs for the GPU with the [CGC backend](../runtime/model-compilation-execution.md) and creates its tokenizer from the model's files. The model must be one that Windows ML supports. |
| Graphs you exported yourself | Loads the graphs, sets a tokenizer, and describes the graphs with `SetComposition`. | Runs each graph on its pipeline backend, composed as you describe, so you don't wire each stage. |
| A GGUF file that you want llama.cpp to run | Loads the file with `IWinMLLlamaModelLoader` and calls `SetModel`. | Runs it on llama.cpp, for text generation only. Use it for a model that the CGC backend doesn't support, or for speculative decoding with a draft model. |

For full control over stages, tensor bindings, and backend options, you can still build the pipelines yourself and configure the Task by hand.

## Requirements

- Windows ML 2.9 experimental or later to specify a model for a Task. Earlier packages don't include these APIs, and changing only the package version number doesn't enable them.
- A GPU with Direct3D 12 for models that run on the CGC backend. These models run on the GPU only, with no CPU fallback. For now, use the [recommended models](../runtime/model-compilation-execution.md#recommended-models).
- C++, C#, or Python. C# needs a temporary wrapper that you copy into your project. A future update will include the C# APIs natively. See [Set up a project](../runtime/get-started.md#configure-your-project).

## Samples

The [Windows ML samples](https://github.com/microsoft/WindowsML) repository includes samples that specify a model for a Task, for text generation, composed text generation, llama.cpp text generation, speech recognition, and decisions, in C++ and Python. C# samples cover text generation, chat completion, speech recognition, and decisions. See [Windows ML samples](../samples.md).

## Next steps

- [Choose how a Task runs your model](./choose-model-path.md)
- [Generate text with your own language model](../runtime/text-generation.md)
- [Chat completion](./chat-completion.md)
- [Recognize speech with your own model](../runtime/speech-recognition.md)
- [Make decisions with typed questions](./decision.md)
