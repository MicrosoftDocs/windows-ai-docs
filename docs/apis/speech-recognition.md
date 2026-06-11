---
title: Speech Recognition with Windows AI APIs
description: Learn how to use the Speech Recognition API to transcribe audio from files or real-time streams using the Windows AI APIs.
ms.topic: how-to
ms.date: 05/28/2026
dev_langs:
- csharp
---

# Speech Recognition

Speech Recognition is an AI-powered on-device speech-to-text technology that transcribes spoken audio into text in real-time or from pre-recorded files. By running entirely on-device, it provides low-latency transcription without requiring a network connection or sending audio data to the cloud. As with all AI models, transcription output may not always be accurate and should be validated for critical use cases.

Adding speech recognition capabilities to your app enables scenarios including the following:

- Real-time captioning and subtitles for meetings or media playback
- Voice-driven note-taking and dictation
- Accessibility features for users who rely on speech input
- Transcription of recorded audio files such as interviews or lectures
- Voice commands and hands-free interaction in desktop applications

The API supports two modes of operation:

- **Batch recognition**: Transcribe a complete audio file in one pass, ideal for pre-recorded content.
- **Streaming recognition**: Continuous real-time transcription from a microphone or audio stream, providing results as phrases are recognized.

You can use [SpeechRecognitionModel](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel) to transcribe speech from audio files or real-time audio streams on-device.

For **API details**, see [API ref for AI Speech features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

[!INCLUDE [AI APIs package manifest requirement](./includes/ai-apis-package-manifest-requirements.md)]

## Prerequisites

- **Windows version:** Windows 11, version 24H2 (build 26100) or later
- **WinAppSDK version:** Version 1.7.1 or later
- **Hardware:** Copilot+ PC with an NPU, **or** any Windows PC meeting the [recommended CPU specifications](#recommended-cpu-specifications)

## Supported hardware

Speech Recognition runs on the following hardware:

| Hardware | Status | Details |
|---|---|---|
| NPU (Copilot+ PC) | ✅ Available | Best performance. Model is preinstalled. See [Copilot+ PCs developer guide](../npu-devices/index.md). |
| CPU | ✅ Available (optional, removable) | Model is not preinstalled — see [Model availability and download](#model-availability-and-download). Best on devices meeting the [recommended CPU specifications](#recommended-cpu-specifications). |
| GPU | ❌ Not supported | Speech Recognition is not available on GPU. |

> [!NOTE]
> **Hardware selection is automatic.** On a Copilot+ PC with an NPU, Speech Recognition always runs on the NPU. On non-Copilot+ devices, it runs on the CPU automatically — there is no developer or end-user opt-in to select CPU on a Copilot+ device. This matches the pattern used by other Windows AI APIs; see [Supported hardware](index.md#supported-hardware) for the cross-API view.

## Recommended CPU specifications

Speech Recognition will run on any CPU that the rest of the Windows AI APIs run on, but real-time transcription quality and latency scale with the host CPU.

For a good CPU experience, target devices that meet **all** of the following recommended specifications:

- **4 or more physical cores**
- **3 GHz or higher base clock**
- **32 MB or more of L3 cache**

These are recommendations, not hard minimums — the API will still attempt to transcribe on lower-spec devices. Apps that target a broad CPU range can use the same runtime CPU check shown for VSR — see [VSR Recommended CPU specifications](video-super-resolution.md#recommended-cpu-specifications) for the C# sample using `System.Management` (WMI). Use the result to gate UX choices, such as defaulting to batch recognition on borderline hardware or hiding the live-streaming entry point.

## Model availability and download

On Copilot+ PCs the speech recognition model is **preinstalled** on the NPU. On CPU-only devices the model is **not** preinstalled — it is downloaded on demand the first time your app calls [**EnsureReadyAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.ensurereadyasync). The download runs in the background through Windows Update. End users can also remove the model later to reclaim disk space.

This behavior matches the model lifecycle used by other optional Windows AI models — your app must handle the "not installed yet" case explicitly rather than assuming the model is present.

### Recommended UX pattern

Because the speech recognition model is downloaded on demand on CPU-only devices, **show a confirmation dialog before calling `EnsureReadyAsync`** so the user can consent to the background download. A typical pattern:

1. Call [**GetReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.getreadystate) and branch on the returned [**AIFeatureReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.aifeaturereadystate):
   - **`Ready`** — the model is installed; proceed.
   - **`NotReady`** or **`EnsureNeeded`** — show your consent dialog (see below), then call `EnsureReadyAsync` only if the user agrees.
   - **`NotSupportedOnCurrentSystem`** — the device does not meet the requirements in [Supported hardware](#supported-hardware). Offer a fallback experience (for example, [Speech Recognition via Windows SDK](/windows/apps/develop/input/speech-recognition) or a cloud-based service) and, when appropriate, surface the hardware requirements so the user can make an informed upgrade decision.
2. In your consent dialog, explain:
   - An optional speech recognition model will be downloaded.
   - The download happens in the background through Windows Update.
   - The user can monitor download progress at **Settings** > **Windows Update**.
   - The user can later remove the model at **Settings** > **System** > **AI Components** if they no longer want it.

   > [!TIP]
   > In user-facing strings (dialog text, status messages), refer to the model as the "**speech recognition model**" or "**optional AI model**" rather than the underlying model name. Most end users aren't familiar with brand names, and generic terms communicate purpose more clearly.
3. While `EnsureReadyAsync` is in progress, show a progress indicator in your app. See [Get started with Windows AI APIs](./get-started.md) for the loading-UI pattern.

### After the model is installed

The model remains on the device until the user removes it. Users manage installed models — including the speech recognition model — at **Settings** > **System** > **AI Components**. If the user later removes the model, your app's next call to `GetReadyState` returns `NotReady` or `EnsureNeeded` and the consent + download flow should be repeated.

## Batch recognition from an audio file

Use batch recognition to transcribe a complete audio file. This approach is ideal for pre-recorded audio content.

1. Call [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.getreadystate) and wait for [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.ensurereadyasync) to complete successfully to confirm that the SpeechRecognitionModel is ready.
2. After the model is ready, call [TryCreateAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.trycreateasync) to instantiate a SpeechRecognitionModel object.
3. Create a [BatchRecognition](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.batchrecognition) instance with the SpeechRecognitionModel.
4. Call [RecognizeFromFile](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.batchrecognition.recognizefromfile) with the path to the audio file to transcribe.

```csharp
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Speech;

if (SpeechRecognitionModel.GetReadyState() != AIFeatureReadyState.Ready)
{
    await SpeechRecognitionModel.EnsureReadyAsync();
}

var speechModelResult = await SpeechRecognitionModel.TryCreateAsync();
if (speechModelResult.SpeechModel == null)
{
    throw new InvalidOperationException(
        $"Failed to create SpeechRecognitionModel: {speechModelResult.ExtendedError}");
}

var speechModel = speechModelResult.SpeechModel;

var batchRecognition = new BatchRecognition(speechModel);
string transcription = await batchRecognition.RecognizeFromFile("path/to/audio.wav");

Console.WriteLine($"Transcription: {transcription}");
```

## Streaming recognition from a real-time audio source

Use streaming recognition to transcribe audio in real-time from a microphone or other audio input device. This approach provides final results as complete phrases are recognized.

1. Call [GetReadyState](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.getreadystate) and wait for [EnsureReadyAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.ensurereadyasync) to complete successfully to confirm that the SpeechRecognitionModel is ready.
2. After the model is ready, call [TryCreateAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.speechrecognitionmodel.trycreateasync) to instantiate a SpeechRecognitionModel object.
3. Create an [AudioConfiguration](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.audioconfiguration) using [FromAudioDevice](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.audioconfiguration.fromaudiodevice) with the microphone device name.
4. Create a [StreamingRecognition](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.streamingrecognition) instance with the AudioConfiguration and SpeechRecognitionModel.
5. Subscribe to the [Recognized](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.streamingrecognition.recognized) event to receive transcription results.
6. Call [StartContinuousRecognitionAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.streamingrecognition.startcontinuousrecognitionasync) to begin transcription.
7. Call [StopContinuousRecognition](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.speech.streamingrecognition.stopcontinuousrecognition) when finished.

```csharp
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Speech;

if (SpeechRecognitionModel.GetReadyState() != AIFeatureReadyState.Ready)
{
    await SpeechRecognitionModel.EnsureReadyAsync();
}

var speechModelResult = await SpeechRecognitionModel.TryCreateAsync();
if (speechModelResult.SpeechModel == null)
{
    throw new InvalidOperationException(
        $"Failed to create SpeechRecognitionModel: {speechModelResult.ExtendedError}");
}

var speechModel = speechModelResult.SpeechModel;

var audioConfig = AudioConfiguration.FromAudioDevice(microphoneDeviceName);
var streamingRecognition = new StreamingRecognition(audioConfig, speechModel);

// Subscribe to receive transcription results as phrases are recognized
streamingRecognition.Recognized += (sender, args) =>
{
    Console.WriteLine($"Recognized: {args.Text}");
};

// Start real-time recognition
await streamingRecognition.StartContinuousRecognitionAsync();

// ... recognition is active, audio is being transcribed in real-time ...

// Stop recognition when done
streamingRecognition.StopContinuousRecognition();
```

## See also

- [Whisper via Foundry Local](/azure/ai-foundry/foundry-local/how-to/how-to-transcribe-audio) (Windows 10+, new model, *performance varies, not available on all devices*)
- [Speech Recognition via Windows SDK](/windows/apps/develop/input/speech-recognition) (Windows 10+, older model, but available on all devices)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)

---

<small>

## THIRD PARTY MODEL NOTICES AND INFORMATION

This API uses components from the [OpenAI Whisper](https://github.com/openai/whisper) model, which is provided under the following license:

MIT License

Copyright (c) 2022 OpenAI

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

</small>
