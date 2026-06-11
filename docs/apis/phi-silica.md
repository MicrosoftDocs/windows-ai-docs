---
title: Get started with Phi Silica in the Windows App SDK
description: Learn about the Phi Silica APIs that ship with the Windows App SDK that you can use to access local language models for local processing and generation of chat, math solving, code generation, reasoning over text, and more.
ms.topic: get-started
ms.date: 01/21/2026
dev_langs:
- csharp
- cpp
---

# Get started with Phi Silica

> [!IMPORTANT]
> The Phi Silica APIs are part of a Limited Access Feature (see [LimitedAccessFeatures class](/uwp/api/windows.applicationmodel.limitedaccessfeatures)). For more information or to request an unlock token, please use the [LAF Access Token Request Form](https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409).

Phi Silica is a powerful hardware-accelerated local language model that provides many capabilities found in Large Language Models (LLMs).  On NPU-equipped devices,the model employs a technique called speculative decoding to accelerate text generation using a smaller draft model that can propose multiple token sequences and be validated in parallel by the main model.

> [!NOTE]
> **Phi Silica features are not available in China.**

Phi Silica is optimized for efficiency and performance on Windows Copilot+ PCs (where it runs on the NPU) and on non-Copilot+ Windows 11 devices with a supported GPU, and can be integrated into your Windows apps through the Windows AI APIs in the Windows App SDK.

This level of optimization is not available in other versions of Phi.

## Supported hardware

Phi Silica runs on the following hardware:

| Hardware | Status | Details |
|---|---|---|
| NPU (Copilot+ PC) | ✅ Available | Best performance. See [Copilot+ PCs developer guide](../npu-devices/index.md). |
| GPU — NVIDIA | ✅ Available | GeForce RTX 30 series and newer with 6+ GB vRAM. |
| GPU — AMD | 🔜 Coming soon | Support for AMD GPUs is planned for a future release. |

> [!IMPORTANT]
> Running Phi Silica on GPU requires **Developer Mode** to be enabled. Go to **Settings** > **System** > **For developers** > **Developer Mode**.
>
> **GPU prerequisites:**
> - **Windows build**: Windows Insider Program Experimental Channel, build 26300.8553 or later
> - **Windows App SDK**: Version 2.2.2-experimental9 (June 2026 Experimental) or later

### GPU driver requirements

Running Phi Silica on GPU **requires** the latest driver installed directly from the GPU manufacturer. Default drivers from Windows Update or OEM installations may not be sufficient and can cause failures or degraded performance.

Download the latest driver for your hardware:

- **NVIDIA GeForce / RTX**: [NVIDIA GeForce 615.21 driver (beta)](https://developer.nvidia.com/downloads/assets/geforce-drivers/615.21_gameready_win11_win10-dch_64bit_international_beta.exe)
- **AMD Radeon**: Coming soon.

> [!NOTE]
> OEM-supplied drivers (delivered through Windows Update or your PC manufacturer's update tool) may overwrite IHV drivers you previously installed. If Phi Silica stops working on GPU after a system update, reinstall the latest driver from the links above.

### GPU feature differences

The following features behave differently on GPU compared to NPU:

- **Prompt compression**: Available on NPU but **not available on GPU**. Applications that rely on prompt compression for longer context windows should account for this when targeting GPU devices.
- **Speculative decoding**: Available on NPU for accelerated text generation. Not currently available on GPU, which may result in lower tokens-per-second throughput.
- **LoRA fine-tuning**: LoRA adapters must be trained in the cloud using the Fine-Tuning Kit (FTK). Inference with your trained adapter can be tested locally using the [AI Dev Gallery](../ai-dev-gallery/index.md). This workflow is the same for both NPU and GPU.

### Model availability and download

Unlike the NPU model — which is pre-installed on Copilot+ PCs — the Phi Silica model for GPU is **not pre-installed on the user's device**. Instead, the model is downloaded on demand the first time your app calls [**EnsureReadyAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.ensurereadyasync). The download is several gigabytes and runs in the background through Windows Update.

#### Recommended UX pattern

Because the Phi Silica GPU model is large, **show a confirmation dialog before calling `EnsureReadyAsync`** so the user can consent to both the storage cost and the background download. A typical pattern:

1. Call [**GetReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.getreadystate) and branch on the returned [**AIFeatureReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.aifeaturereadystate):
   - **`Ready`** — the model is installed; proceed.
   - **`NotReady`** or **`EnsureNeeded`** — show your consent dialog (see below), then call `EnsureReadyAsync` only if the user agrees.
   - **`NotSupportedOnCurrentSystem`** — the user's hardware does not meet the requirements in [Supported hardware](#supported-hardware). Offer a fallback experience and, when appropriate, surface the hardware requirements so the user can make an informed upgrade decision.
2. In your consent dialog, explain:
   - An optional language model will be downloaded (several GB of storage).
   - The download happens in the background through Windows Update.
   - The user can monitor download progress at **Settings** > **Windows Update**.
   - The user can later remove the model at **Settings** > **System** > **AI Components** if they no longer want it.

   > [!TIP]
   > In user-facing strings (dialog text, status messages), refer to the model as the "**language model**" or "**optional AI model**" rather than "Phi Silica." Most end users aren't familiar with the brand name, and generic terms communicate purpose more clearly.
3. While `EnsureReadyAsync` is in progress, show a progress indicator in your app. The returned operation exposes a status option that drives a loading UI; see [Get started with Windows AI APIs](./get-started.md) for details.

#### After the model is installed

The model remains on the device until the user removes it. Users manage installed models at **Settings** > **System** > **AI Components**, where the Phi Silica GPU model appears as **"AI LanguageModel"**. If the user later removes the model, your app's next call to `GetReadyState` returns `NotReady` or `EnsureNeeded` and the consent + download flow should be repeated.

For API details, see:

- [microsoft.windows.ai](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai)
- [microsoft.windows.ai.imaging](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging)
- [microsoft.windows.ai.text](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text)

## Integrate Phi Silica

With a local Phi Silica language model you can generate text responses to user prompts. First, ensure you have the pre-requisites and models available on your device as outlined in [Getting Started with Windows AI APIs](index.md).

### Specify the required namespaces

To use Phi Silica, make sure you are using the required namespaces:

```csharp
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Text;
```

```cppwinrt
#include "winrt/Microsoft.Windows.AI.Text.h"
using namespace Microsoft::Windows::AI;
using namespace Microsoft::Windows::AI::Text;
```

### Generate a response

This example shows how to generate a response to a Q&A prompt with custom content moderation (see [Content Moderation with the Windows AI APIs](./content-moderation.md)).

1. Ensure the language model is available by calling the [**GetReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.getreadystate) method and waiting for the [**EnsureReadyAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.ensurereadyasync) method to return successfully.

2. Once the language model is available, create a [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) object to reference it.

3. Submit a string prompt to the model using the [**GenerateResponseAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.generateresponseasync) method, which returns the complete result.

```csharp
if (LanguageModel.GetReadyState() == AIFeatureReadyState.NotReady) 
{ 
   var op = await LanguageModel.EnsureReadyAsync(); 
} 

using LanguageModel languageModel = await LanguageModel.CreateAsync();

string prompt = "Provide the molecular formula for glucose.";

LanguageModelOptions options = new LanguageModelOptions();
ContentFilterOptions filterOptions = new ContentFilterOptions();
filterOptions.PromptMaxAllowedSeverityLevel.Violent = SeverityLevel.Minimum;
options.ContentFilterOptions = filterOptions;

var result = await languageModel.GenerateResponseAsync(prompt, options);
 
Console.WriteLine(result.Text);
```

```cppwinrt
if (LanguageModel::GetReadyState() == AIFeatureReadyState::NotReady)
{
    auto op = LanguageModel::EnsureReadyAsync().get();
}

auto languageModel = LanguageModel::CreateAsync().get();

const winrt::hstring prompt = L"Provide the molecular formula for glucose.";

LanguageModelResponseResult result = languageModel.GenerateResponseAsync(prompt).get();
std::cout << result.Text().c_str() << std::endl;
```

The response generated by this example is:

```output
C6H12O6
```

### Text Intelligence Skills

Phi Silica includes built-in text transformation capabilities (known as Text Intelligence Skills) that can deliver structured, concise, and user-friendly responses through predefined formatting using a local language model.

Supported skills include:

- Text-to-table: Formats the prompt response into a structured table format, when appropriate.
- Summarize: Returns a concise summary of the prompt text.
- Rewrite: Rephrases the prompt text to optimize clarity, readability, and, when specified, tone (or style).

The following steps describe how to use Text Intelligence Skills.

1. **Create a LanguageModel object**  
    This object references the local Phi Silica language model (remember to confirm that the Phi Silica model is available on the device).

1. **Instantiate the skill-specific object**  
    Choose the appropriate class based on the skill you want to apply and pass the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) instance as a parameter.

1. **Call the method to perform the skill**  
    Each skill exposes an asynchronous method that processes the input and returns a formatted result.

1. **Handle the response**  
    The result is returned as a typed object, which you can print or log as needed.

This example demonstrates the text summarizing skill.

1. Create a [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) instance (`languageModel`).
1. Pass that [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) to the [**TextSummarizer**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer) constructor.
1. Pass some text to the [**SummarizeAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer.summarizeasync) method and print the result.

```csharp
using namespace Microsoft.Windows.AI.Text;

using LanguageModel languageModel = await LanguageModel.CreateAsync();

var textSummarizer = new TextSummarizer(languageModel);
string text = @"This is a large amount of text I want to have summarized.";
var result = await textSummarizer.SummarizeAsync(text);

Console.WriteLine(result.Text); 
```

```cppwinrt
using namespace Microsoft::Windows::AI::Text;

auto languageModel = LanguageModel::CreateAsync().get();
auto textSummarizer = TextSummarizer(languageModel);
std::string prompt = "This is a large amount of text I want to have summarized.";
auto result = textSummarizer.SummarizeAsync(prompt);

std::wcout << result.get().Text() << std::endl;
```

---

## Responsible AI

We've followed core principles and practices described in the [Microsoft Responsible AI Standards](https://www.microsoft.com/ai/principles-and-approach) to ensure these APIs are trustworthy, secure, and built responsibly. For more details on implementing AI features in your app, see [Responsible Generative AI Development on Windows](/windows/ai/rai).

### GPU transparency notes

For detailed information about the capabilities, limitations, and responsible use of Phi Silica on non-Copilot+ PCs (GPU), see the [Transparency Note: Phi Silica on Non-Copilot+ PCs](phi-silica-transparency-note.md).

Key differences between NPU and GPU execution:

| Factor | Copilot+ PCs (NPU) | Non-Copilot+ PCs (GPU) |
|--------|--------------------|-----------------------------|
| **Inference Latency** | Optimized; low latency via NPU acceleration and speculative decoding | Higher latency; depends on GPU generation, VRAM, and current GPU load |
| **Power Consumption** | NPU is power-efficient, suitable for battery-powered use | Higher power consumption; may impact battery life on laptops |
| **Prompt Compression** | ✅ Available | ❌ Not available on GPU |
| **Speculative Decoding** | ✅ Available | ❌ Not available on GPU |
| **Model Optionality** | Model is managed by the system | Model is downloaded on demand and can be removed via **Settings** > **System** > **AI Components** |

##### Operational Factors for Non-Copilot+ PCs

- **Hardware diversity**: Non-Copilot+ PCs span a wide range of GPU configurations. Performance will vary significantly across this device spectrum.
- **Minimum hardware requirements**: Devices must meet minimum GPU and memory requirements to run Phi Silica.
- **Software dependencies**: Non-Copilot+ PC execution requires the latest IHV GPU driver installed directly from the GPU manufacturer (NVIDIA). Default drivers from Windows Update or OEM installations may not be sufficient and can cause failures or degraded performance.

#### System Performance

##### Understanding Performance on Non-Copilot+ PCs

Phi Silica's output quality (accuracy, coherence, relevance) is consistent across Copilot+ and non-Copilot+ PCs because the same model weights and architecture are used. The primary differences are in **inference speed**, **resource consumption**, and **user experience responsiveness**.

Developers should:

- **Benchmark on representative hardware**: Test on a range of non-Copilot+ devices that reflect your target user base, including lower-end configurations.
- **Set user expectations**: Clearly communicate that response times may vary based on device hardware. Consider showing progress indicators or streaming partial results.
- **Implement timeouts and fallbacks**: For scenarios where response time is critical, implement appropriate timeouts and consider offering cloud-based fallback options (with user consent).
- **Monitor resource usage**: Track GPU utilization, VRAM consumption, and system memory usage during inference to identify and address performance bottlenecks.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
