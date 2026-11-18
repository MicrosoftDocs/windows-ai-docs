---
title: Recognize speech with your own model using Windows ML
description: Use the experimental Windows ML Speech Recognition API to transcribe audio with a speech model you bring, such as Whisper or Nemotron.
author: andrewleader
ms.author: aleader
ms.date: 11/18/2026
ms.topic: concept-article
dev_langs:
- cpp
- csharp
- python
---

# Recognize speech with your own model using Windows ML

> [!IMPORTANT]
> The Windows ML Runtime and Speech Recognition APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

> [!IMPORTANT]
> The Windows ML Runtime and Speech Recognition APIs are currently available in C++ and Python. C# is supported through a temporary wrapper that you copy into your project; a future update will include the C# APIs natively. See [Set up a project](./get-started.md#configure-your-project).

The Windows ML Speech Recognition API simplifies running automatic speech recognition (ASR) over your own speech model, on top of the [Windows ML Runtime API](./overview.md). It handles the transcription loop, streaming output, and cancellation for you. Windows doesn't ship a model for this API; you choose, package, and distribute the model with your app. It's one of the [Windows ML Task APIs](../tasks/overview.md).

> [!TIP]
> If you don't need a specific speech model or custom deployment and just want on-device speech-to-text with a model Microsoft manages, use the ready-to-use [Speech Recognition API](/windows/ai/apis/speech-recognition) instead.

There are two ways to specify a model for speech recognition:

- **From a checkpoint folder (Windows ML 2.9).** Load a published speech model folder, such as Whisper or Nemotron, with `LoadModelFromPath` and call `SetModel` with a GPU execution target. Windows ML compiles the model for the GPU with the [CGC backend](./model-compilation-execution.md). Pass the model's tokenizer explicitly. See [Choose how a Task runs your model](../tasks/choose-model-path.md).
- **From pipelines you build.** For a Whisper model, create the underlying Runtime objects — the encoder and decoder models, the execution target, and the pipelines — and pass them to the session. This path doesn't hide hardware placement; it only removes the need to write your own decode loop.

## Supported models

The checkpoint-folder path takes a published speech model folder. The folder needs `config.json`, the weights in `model.safetensors` (or `model.safetensors.index.json` with every shard it names), and the tokenizer files, such as `tokenizer.json` and `tokenizer_config.json`. A Whisper folder also needs `generation_config.json`, unless `config.json` holds `no_timestamps_token_id`. A model that Windows ML doesn't support fails with `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`.

The pipeline path is specific to the **Whisper** encoder/decoder architecture. You supply **ONNX** encoder and decoder models (`encoder_model.onnx` and `decoder_model.onnx`), which run through ONNX Runtime, plus a tokenizer file.

Sessions from a checkpoint folder accept mono waveforms at the model's sample rate, or at a rate from 8,000 through 48,000 Hz when the model converts its input. The input tensor holds mono floating-point PCM samples plus sample-rate and valid-length metadata.

> [!NOTE]
> When Windows ML runs a model with the CGC backend, start with the [recommended speech models](./model-compilation-execution.md#recommended-models): Whisper Medium, Whisper tiny (the samples' model), and Nemotron 3.5 ASR Streaming 0.6B. Other speech models aren't validated yet.

## Live input

A model that accepts audio as your app records it, such as a streaming model like Nemotron, exposes a live session (`IWinMLAutomaticSpeechRecognitionLiveSession`). You start live input, write audio chunks as you capture them, and read transcript fragments as the model commits them. Live input uses bounded backpressure: `WriteChunk` reports whether the Task accepted the chunk. Query the session for the live interface to test support, because a model that recognizes speech in fixed-length windows, such as Whisper, doesn't expose it.

## Language, mode, and predictions

A speech recognition configuration can set a language, a mode, and whether to report predictions. Language support depends on the model.

- **Mode.** `TRANSCRIBE`, the default, writes the speech as text in the language spoken. `TRANSLATE` writes it as English text. Sessions created from a checkpoint folder transcribe only in Windows ML 2.9, so `TRANSLATE` fails `CreateSession` with `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`. The encoder-decoder composition translates when its tokenizer maps `<|translate|>` to identifier 50358.
- **Predictions.** Opt in to provisional text that follows the recognized text and that later recognition might revise. It's off by default. A model that recognizes speech in fixed-length windows, such as Whisper, and the encoder-decoder composition report no predictions. Without predictions, each update delivers the text of a segment as the model commits it, and Windows ML never revises it. Every segment after the first starts with one space, so the fragments of an operation concatenate into its transcript.

> [!NOTE]
> The Whisper-specific speech recognition names in Windows ML 2.7, such as the Whisper configuration and input interfaces, are now encoder-decoder names, with the same interface IDs. The Python projection no longer includes `whisper_bindings`, `transcribe_whisper_log_mel`, or `WhisperAutomaticSpeechRecognitionBindings`. Code that implements `IWinMLAutomaticSpeechRecognitionConfiguration` must be rebuilt, because the interface gained language, mode, and prediction methods.

## Transcribe from a checkpoint folder

Load the speech model's checkpoint folder with `LoadModelFromPath`, create the Task with the model's tokenizer, and call `SetModel` with a GPU execution target. The examples use Whisper tiny; point them at another supported speech model folder, such as Nemotron, to use that model. Unlike text generation, speech recognition takes the tokenizer explicitly. Set the language and mode, then create a session:

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
using Model model = runtime.LoadModelFromPath(@"C:\models\whisper-tiny");
using Tokenizer tokenizer = Tokenizer.CreateFromFile(@"C:\models\whisper-tiny\tokenizer.json");

using WinMLTasks tasks = WinMLTasks.Create(runtime);
using AutomaticSpeechRecognitionTask task =
    tasks.AutomaticSpeechRecognition.CreateAutomaticSpeechRecognitionTask(tokenizer);

// Bind the model to the configuration on the GPU target.
using SpeechRecognitionConfiguration configuration = task.CreateConfiguration();
using (ModelSpeechRecognitionConfiguration modelConfiguration =
    configuration.As<ModelSpeechRecognitionConfiguration>())
{
    modelConfiguration.SetModel(target, model);
}

// Without a language, the model's default applies.
configuration.Language = "en";
configuration.Mode = SpeechRecognitionMode.Transcribe;
configuration.Validate();

// A SpeechRecognizer keeps the session on a thread of its own and streams the transcript.
await using SpeechRecognizer recognizer =
    await SpeechRecognizer.CreateAsync(tasks, task, configuration);
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

ComPtr<IWinMLModel> model;
THROW_IF_FAILED(runtime->LoadModelFromPath(
    L"C:\\models\\whisper-tiny", nullptr, model.GetAddressOf()));
ComPtr<IWinMLTokenizer> tokenizer;
THROW_IF_FAILED(WinMLCreateTokenizerFromFile(
    L"C:\\models\\whisper-tiny\\tokenizer.json", tokenizer.GetAddressOf()));

ComPtr<IWinMLTasks> tasks;
THROW_IF_FAILED(WinMLCreateTasks(runtime.Get(), IID_PPV_ARGS(tasks.GetAddressOf())));
ComPtr<IWinMLAutomaticSpeechRecognitionTaskFactory> factory;
THROW_IF_FAILED(tasks.As(&factory));
ComPtr<IWinMLAutomaticSpeechRecognitionTask> task;
THROW_IF_FAILED(factory->CreateAutomaticSpeechRecognitionTask(
    tokenizer.Get(), task.GetAddressOf()));

// Bind the model to the configuration on the GPU target.
ComPtr<IWinMLAutomaticSpeechRecognitionConfiguration> configuration;
THROW_IF_FAILED(task->CreateConfiguration(configuration.GetAddressOf()));
ComPtr<IWinMLModelAutomaticSpeechRecognitionConfiguration> modelConfiguration;
THROW_IF_FAILED(configuration.As(&modelConfiguration));
THROW_IF_FAILED(modelConfiguration->SetModel(target.Get(), model.Get(), nullptr));

// Without a language, the model's default applies.
THROW_IF_FAILED(configuration->SetLanguage(L"en"));
THROW_IF_FAILED(configuration->SetMode(WINML_AUTOMATIC_SPEECH_RECOGNITION_MODE_TRANSCRIBE));
THROW_IF_FAILED(configuration->Validate());

ComPtr<IWinMLAutomaticSpeechRecognitionSession> session;
THROW_IF_FAILED(task->CreateSession(configuration.Get(), session.GetAddressOf()));
```

---

To stream a transcript from either path, call `TranscribeWaveform` as the next section shows, with the C# `SpeechRecognizer` streaming `SpeechRecognitionUpdate` values with `await foreach`.

## Transcribe audio with a Whisper pipeline

The following example uses the Whisper encoder-decoder composition. Supply a Runtime tensor holding waveform samples. Load the Whisper encoder and decoder models, build a pipeline for each, and compose them with a tokenizer into a session:

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

// Load the Whisper encoder as its own single-stage pipeline.
using Model encoderModel = runtime.LoadModelFromFile(@"C:\models\whisper\encoder_model.onnx");
using PipelineBuilder encoderBuilder = runtime.CreatePipelineBuilder();
using Stage encoderStage = encoderBuilder.AddModelStage(encoderModel, target, "whisper-encoder");
using Pipeline encoderPipeline = encoderBuilder.Build();

// Load the Whisper decoder the same way.
using Model decoderModel = runtime.LoadModelFromFile(@"C:\models\whisper\decoder_model.onnx");
using PipelineBuilder decoderBuilder = runtime.CreatePipelineBuilder();
using Stage decoderStage = decoderBuilder.AddModelStage(decoderModel, target, "whisper-decoder");
using Pipeline decoderPipeline = decoderBuilder.Build();

using Tokenizer tokenizer = Tokenizer.CreateFromFile(@"C:\models\whisper\tokenizer.json");

// Compose the encoder/decoder pipelines and tokenizer into a session.
using WinMLTasks tasks = WinMLTasks.Create(runtime);
using AutomaticSpeechRecognitionTask task =
    tasks.AutomaticSpeechRecognition.CreateAutomaticSpeechRecognitionTask(tokenizer);
using SpeechRecognitionConfiguration configuration = task.CreateConfiguration();
using (EncoderDecoderSpeechRecognitionConfiguration whisper =
    configuration.As<EncoderDecoderSpeechRecognitionConfiguration>())
{
    whisper.SetBindings(new EncoderDecoderSpeechRecognitionBindings
    {
        EncoderPipeline = encoderPipeline,
        EncoderStage = encoderStage,
        DecoderPipeline = decoderPipeline,
        DecoderStage = decoderStage,
        TensorTarget = target,
    });
}

configuration.Validate();

// A SpeechRecognizer keeps the session on a thread of its own and streams the transcript.
await using SpeechRecognizer recognizer =
    await SpeechRecognizer.CreateAsync(tasks, task, configuration);
```

### [C++](#tab/cpp)

```cpp
// Create the runtime and an execution target (CPU here; GPU/NPU also available).
ComPtr<IWinMLRuntime> runtime;
THROW_IF_FAILED(WinMLCreateRuntime(IID_PPV_ARGS(runtime.GetAddressOf())));
ComPtr<IWinMLExecutionTarget> target;
THROW_IF_FAILED(runtime->CreateCpuExecutionTarget(target.GetAddressOf()));

// Load the Whisper encoder as its own single-stage pipeline.
ComPtr<IWinMLModel> encoderModel;
THROW_IF_FAILED(runtime->LoadModelFromFile(
    L"C:\\models\\whisper\\encoder_model.onnx", nullptr, encoderModel.GetAddressOf()));
ComPtr<IWinMLPipelineBuilder> encoderBuilder;
THROW_IF_FAILED(runtime->CreatePipelineBuilder(encoderBuilder.GetAddressOf()));
ComPtr<IWinMLStage> encoderStage;
THROW_IF_FAILED(encoderBuilder->AddModelStage(
    encoderModel.Get(), target.Get(), L"whisper-encoder", encoderStage.GetAddressOf()));
ComPtr<IWinMLPipeline> encoderPipeline;
THROW_IF_FAILED(encoderBuilder->Build(encoderPipeline.GetAddressOf()));

// Load the Whisper decoder the same way.
ComPtr<IWinMLModel> decoderModel;
THROW_IF_FAILED(runtime->LoadModelFromFile(
    L"C:\\models\\whisper\\decoder_model.onnx", nullptr, decoderModel.GetAddressOf()));
ComPtr<IWinMLPipelineBuilder> decoderBuilder;
THROW_IF_FAILED(runtime->CreatePipelineBuilder(decoderBuilder.GetAddressOf()));
ComPtr<IWinMLStage> decoderStage;
THROW_IF_FAILED(decoderBuilder->AddModelStage(
    decoderModel.Get(), target.Get(), L"whisper-decoder", decoderStage.GetAddressOf()));
ComPtr<IWinMLPipeline> decoderPipeline;
THROW_IF_FAILED(decoderBuilder->Build(decoderPipeline.GetAddressOf()));

ComPtr<IWinMLTokenizer> tokenizer;
THROW_IF_FAILED(WinMLCreateTokenizerFromFile(
    L"C:\\models\\whisper\\tokenizer.json", tokenizer.GetAddressOf()));

// Compose the encoder/decoder pipelines and tokenizer into a session.
const winml::tasks::speech::asr::AutomaticSpeechRecognitionArguments arguments{
    runtime.Get(),
    tokenizer.Get(),
    {
        encoderPipeline.Get(),
        encoderStage.Get(),
        decoderPipeline.Get(),
        decoderStage.Get(),
        target.Get(),
    },
};
winml::tasks::speech::asr::AutomaticSpeechRecognition speechRecognition;
THROW_IF_FAILED(winml::tasks::speech::asr::BuildAutomaticSpeechRecognition(
    arguments, speechRecognition));
```

---

Call `TranscribeWaveform` to start a streaming operation, then pull fragments from the returned stream until it reports completion:

### [C#](#tab/csharp)

```csharp
// Cancelling the token stops transcription in the Runtime.
CancellationTokenSource cancellation = new();

// Read transcript text one fragment at a time until the stream completes.
await foreach (SpeechRecognitionUpdate update in
    recognizer.TranscribeWaveformAsync(waveform, waveformMetadata, cancellation.Token))
{
    Console.Write(update.Text);
}

// The outcome is the authoritative transcript and finish reason.
SpeechRecognitionOutcome outcome = recognizer.LastOutcome!;
Console.WriteLine($"\nFinished because: {outcome.FinishReason}");

// Closing the session reports a failure that disposing it would not.
await recognizer.CloseAsync();
```

### [C++](#tab/cpp)

```cpp
ComPtr<IWinMLCancellationSource> cancellation;
THROW_IF_FAILED(speechRecognition.tasks->CreateCancellationSource(
    cancellation.GetAddressOf()));

ComPtr<IWinMLAutomaticSpeechRecognitionPullStream> stream;
THROW_IF_FAILED(speechRecognition.session->TranscribeWaveform(
    waveform.Get(),
    &waveformMetadata,
    cancellation.Get(),
    stream.GetAddressOf()));

// Pull transcript text one fragment at a time until the stream completes.
WINML_AUTOMATIC_SPEECH_RECOGNITION_READ_STATUS status{};
LPCWSTR fragment = nullptr;
while (SUCCEEDED(stream->ReadNext(&status, &fragment)) &&
    status == WINML_AUTOMATIC_SPEECH_RECOGNITION_READ_STATUS_UPDATE)
{
    std::wprintf(L"%ls", fragment);
}

// The terminal result is the authoritative transcript and finish reason.
ComPtr<IWinMLAutomaticSpeechRecognitionResult> result;
THROW_IF_FAILED(stream->GetResult(result.GetAddressOf()));
```

---

`waveform` and `waveformMetadata` come from your own audio-loading code: a Runtime tensor holding mono floating-point PCM samples, plus the sample rate and valid-length metadata that describes it. In C#, `target.AudioTensorFactory` creates audio tensors.

The completed result reports the full transcript and a finish reason, such as an end-of-transcript token, cancellation, or an error.

## C#, C++, and Python

The native API is exposed through `WinMLTasks.h` and the typed C++ helpers under `winml/tasks/speech/asr/WhisperAutomaticSpeechRecognition.hpp`. Python applications use the `windowsml.tasks` projection together with `windowsml.runtime`. C# applications use CsWin32 bindings through a wrapper that you copy into your project for now; see [Set up a project](./get-started.md#configure-your-project).

The [Windows ML samples](../samples.md) include C++, C#, and Python speech recognition samples for both ways of specifying a model. The samples show the full Runtime setup beside the speech-recognition configuration so you can see where the abstraction begins and how to customize the underlying workflow.

## See also

- [Speech Recognition with Windows AI APIs](/windows/ai/apis/speech-recognition) (ready-to-use, Microsoft-managed model)
- [Windows ML Task APIs overview](../tasks/overview.md)
- [Choose how a Task runs your model](../tasks/choose-model-path.md)
- [Windows ML Runtime API overview](./overview.md)
- [Windows ML Runtime API concepts](./concepts.md)
- [Generate text with the Windows ML Text Generation API](./text-generation.md)
- [Windows ML samples](../samples.md)
