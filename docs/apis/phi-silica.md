---
title: Get started with Phi Silica in the Windows App SDK
description: Learn about the new Phi Silica APIs that will ship with the Windows App SDK and can be used to access local language models for local processing and generation of chat, math solving, code generation, reasoning over text, and more.
ms.topic: get-started
ms.date: 08/21/2025
dev_langs:
- csharp
- cpp
---

# Get started with Phi Silica

> [!IMPORTANT]
> The Phi Silica APIs are part of a Limited Access Feature (see [LimitedAccessFeatures class](/uwp/api/windows.applicationmodel.limitedaccessfeatures)). For more information or to request an unlock token, please use the [LAF Access Token Request Form](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRziEs9BG8idOn69ZIJAQg8tUN1BXSldDS1dPMVZZNEpJREhISkZYM1dWTS4u&route=shorturl).

Phi Silica is a local language model that you can integrate into your Windows apps using Windows AI Foundry.

> [!NOTE]
> **Phi Silica features are not available in China.**

As Microsoft's most powerful NPU-tuned local language model, Phi Silica is optimized for efficiency and performance on Windows Copilot+ PCs devices while still offering many of the capabilities found in Large Language Models (LLMs).

This level of optimization is exclusive to the model within the Windows App SDK and is not available in other versions of Phi. For API details, see:

- [microsoft.windows.ai](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai)
- [microsoft.windows.ai.imaging](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging)
- [microsoft.windows.ai.text](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text)

> [!IMPORTANT]
> The following is a list of Windows AI features and the Windows App SDK release in which they are currently supported.
>
> [**Version 1.8 Experimental (1.8.0-experimental1)**](/windows/apps/windows-app-sdk/experimental-channel#version-18-experimental-180-experimental1) - [LoRA fine-tuning for Phi Silica](phi-silica-lora.md)
>
> [**Version 1.8 Preview (1.8.0-preview)**](/windows/apps/windows-app-sdk/preview-channel#version-18-preview-18-preview) - [Object Erase](imaging.md#what-can-i-do-with-object-erase), [Phi Silica](phi-silica.md), [Conversation Summarization (Text Intelligence)](phi-silica.md#text-intelligence-skills)
>
> [**Private preview**](https://aka.ms/WindowsAIFSemanticSearch) - Semantic Search
>
> [**Version 1.7.1 (1.7.250401001)**](/windows/apps/windows-app-sdk/stable-channel#version-171-17250401001) - All other APIs
>
> These APIs will only be functional on Windows Insider Preview (WIP) devices that have received the May 7th update. On May 28-29, an optional update will be released to non-WIP devices, followed by the Jun 10 update. This update will bring with it the AI models required for the Windows AI APIs to function. These updates will also require that any app using Windows AI APIs will be unable to do so until the app has been granted package identity at runtime.

## Integrate Phi Silica

With a local Phi Silica language model you can generate text responses to user prompts. First, ensure you have the pre-requisites and models available on your device as outlined in [Getting Started with Windows AI APIs](index.md).

### Specify the required namespaces

To use Phi Silica, make sure you are using the required namespaces:

```csharp
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Text;
```

```cpp
#include "winrt/Microsoft.Windows.AI.Text.h"
using namespace Microsoft::Windows::AI;
using namespace Microsoft::Windows::AI::Text;
```

### Generate a response

This example shows how to generate a response to a Q&A prompt with custom content moderation (see [Content Moderation with Windows AI Foundry](./content-moderation.md)).

1. Ensure the language model is available by calling the [**GetReadyState**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.getreadystate) method and waiting for the [**EnsureReadyAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.ensurereadyasync) method to return successfully.

2. Once the language model is available, create a [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) object to reference it.

3. Submit a string prompt to the model using the [**GenerateResponseAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.generateresponseasync) method, which returns the complete result.

```csharp
if (LanguageModel.GetReadyState() == AIFeatureReadyState.EnsureNeeded) 
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

```cpp
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

- Text-to-table: Converts the prompt response into a structured table format, where applicable.
- Summarize: Returns a concise summary of the prompt text.
- Rewrite: Rephrases the prompt response to improve clarity and readability.

The following steps describe how to use Text Intelligence Skills.

1. **Create a LanguageModel object**  
    This object references the local Phi Silica language model (remember to confirm that the Phi Silica model is available on the device).

1. **Instantiate the skill-specific object**  
    Choose the appropriate class based on the skill you want to apply and pass the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) instance as a parameter.

1. **Call the method to perform the skill**  
    Each skill exposes an asynchronous method that processes the input and returns a formatted result.

1. **Handle the response**  
    The result is returned as a typed object, which you can be printed or logged as needed.

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

```cpp
using namespace Microsoft::Windows::AI::Text;

auto languageModel = LanguageModel::CreateAsync().get();
auto textSummarizer = TextSummarizer(languageModel);
std::string prompt = "This is a large amount of text I want to have summarized.";
auto result = textSummarizer.SummarizeAsync(prompt);

std::wcout << result.get().Text() << std::endl;
```

---

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](../rai.md) when implementing AI features in your app.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
