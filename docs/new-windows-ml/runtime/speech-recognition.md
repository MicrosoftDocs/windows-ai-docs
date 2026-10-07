---
title: Recognize speech with your own Whisper model using Windows ML
description: Use the experimental Windows ML Speech Recognition API to transcribe audio with a Whisper model you bring yourself, running over a Windows ML Runtime pipeline.
ms.date: 09/28/2026
ms.topic: concept-article
dev_langs:
- cpp
- python
---

# Recognize speech with your own Whisper model using Windows ML

> [!IMPORTANT]
> The Windows ML Runtime and Speech Recognition APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

> [!IMPORTANT]
> The Windows ML Runtime and Speech Recognition APIs are currently available in C++ and Python; **C# is currently not supported.** Let us know if you're interested in using these APIs from C# by [posting an issue in the Windows ML GitHub repo](https://github.com/microsoft/WindowsML/issues)!

The Windows ML Speech Recognition API simplifies running automatic speech recognition (ASR) over your own Whisper model. It composes a Whisper encoder pipeline, a decoder pipeline, and a tokenizer, then handles the autoregressive transcription loop, streaming output, and cancellation for you, on top of the [Windows ML Runtime API](./overview.md). Windows doesn't ship a model for this API; you choose, package, and distribute the Whisper model — encoder and decoder files, plus a tokenizer — with your app.

> [!TIP]
> If you don't need a specific Whisper model or custom deployment and just want on-device speech-to-text with a model Microsoft manages, use the ready-to-use [Speech Recognition API](/windows/ai/apis/speech-recognition) instead.

You still create the underlying Runtime objects — the encoder and decoder models, the execution target, and the pipelines — and pass them to the session. The API doesn't hide hardware placement; it only removes the need to write your own decode loop.

## Supported models

The Speech Recognition API is built specifically for the **Whisper** encoder/decoder architecture — it isn't a generic ASR API. You supply:

- **ONNX** encoder and decoder models (`encoder_model.onnx` and `decoder_model.onnx`), run through ONNX Runtime

A tokenizer file accompanies the encoder/decoder pair. The input tensor holds mono floating-point PCM samples plus sample-rate and valid-length metadata.

## Transcribe audio

Supply a Runtime tensor holding waveform samples. Load the Whisper encoder and decoder models, build a pipeline for each, and compose them with a tokenizer into a session:

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

Call `TranscribeWaveform` to start a streaming operation, then pull fragments from the returned stream until it reports completion:

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

`waveform` and `waveformMetadata` come from your own audio-loading code: a Runtime tensor holding mono floating-point PCM samples, plus the sample rate and valid-length metadata that describes it.

The completed result reports the full transcript and a finish reason, such as an end-of-transcript token, cancellation, or an error.

## C++ and Python

The native API is exposed through `WinMLTasks.h` and the typed C++ helpers under `winml/tasks/speech/asr/WhisperAutomaticSpeechRecognition.hpp`. Python applications use the `windowsml.tasks` projection together with `windowsml.runtime`.

The [Windows ML samples](../samples.md) include a C++ and Python Whisper transcription sample. The sample shows the full Runtime setup beside the speech-recognition configuration so you can see where the abstraction begins and how to customize the underlying workflow.

## See also

- [Speech Recognition with Windows AI APIs](/windows/ai/apis/speech-recognition) (ready-to-use, Microsoft-managed model)
- [Windows ML Runtime API overview](./overview.md)
- [Windows ML Runtime API concepts](./concepts.md)
- [Generate text with the Windows ML Text Generation API](./text-generation.md)
- [Windows ML samples](../samples.md)
