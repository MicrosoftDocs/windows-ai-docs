---
title: Get started with Phi Silica in the Windows App SDK
description: Learn about the new Phi Silica APIs that will ship with the Windows App SDK and can be used to access local language models for local processing and generation of chat, math solving, code generation, reasoning over text, and more.
ms.topic: article
ms.date: 04/14/2025
dev_langs:
- csharp
- cpp
---

# Get started with Phi Silica

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.
>
> - Phi Silica is not available in China.

Phi Silica is a local language model that you can integrate into your Windows apps using the [Windows App SDK](/windows/apps/windows-app-sdk/).

As Microsoft's most powerful NPU-tuned local language model, Phi Silica is optimized for efficiency and performance on Windows Copilot+ PCs devices while still offering many of the capabilities found in Large Language Models (LLMs).

This level of optimization is exclusive to the model within the Windows App SDK and is not available in other versions of Phi. For **API details**, see [API ref for Phi Silica in the Windows App SDK](phi-silica-api-ref.md).

## Phi Silica Walkthrough

This short tutorial will walk you through a sample that uses Phi Silica in a .NET MAUI app. To start, ensure you've completed the steps in the [Getting Started page.](get-started.md)

### Introduction
This sample demonstrates use of some Windows Copilot Runtime APIs, including LanguageModel for text generation and ImageScaler for image super resolution to scale and sharpen images. Click one of the "Scale" buttons to scale the image (or reshow the original, unscaled image), or enter a text prompt and click the "Generate" button to generate a text response.

The changes from the ".NET MAUI App" template are split across four files:
1. MauiWindowsCopilotRuntimeSample.csproj: Adds the required Windows App SDK package reference for the Windows Copilot Runtime APIs. This reference needs to be conditioned only when building for Windows (see Additional Notes below for details). This file also sets the necessary TargetFramework for Windows.
2. Platforms/Windows/MainPage.cs: Implements partial methods from the shared MainPage class to show and handle the text generation and image scaling functionality.
3. MainPage.xaml: Defines controls to show text generation and image scaling.
4. MainPage.xaml.cs: Defines partial methods which the Windows-specific MainPage.cs implements.

In the second file listed above, you'll find the following function, which demonstrates some basic functionality for the LanguageModel method:
```
        private async void DoGenerateTextFromEntryPrompt()
        {
            if (!LanguageModel.IsAvailable())
            {
                answer.Text = "Making LanguageModel available...";
                var op = await LanguageModel.MakeAvailableAsync();
            }

            answer.Text = "Preparing LanguageModel...";
            using LanguageModel languageModel = await LanguageModel.CreateAsync();

            string prompt = entryPrompt.Text;

            answer.Text = "Generating response...";
            Windows.Foundation.AsyncOperationProgressHandler<LanguageModelResponse, string>
            progressHandler = (asyncInfo, delta) =>
            {
                System.Diagnostics.Debug.WriteLine($"Progress: {delta}");
                var fullResponse = asyncInfo.GetResults().Response;
                System.Diagnostics.Debug.WriteLine($"Response so far: {fullResponse}");
                var newText = "Q: " + prompt + "\nA:" + fullResponse;
                answer.Dispatcher.Dispatch(() => { answer.Text = newText; });
            };

            var asyncOp = languageModel.GenerateResponseWithProgressAsync(prompt);

            asyncOp.Progress = progressHandler;

            var result = await asyncOp;
            System.Diagnostics.Debug.WriteLine("DONE: " + result.Response);
        }
```

### Build and run the sample
1. Clone the [repository](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsCopilotRuntime/cs-maui) onto your Copilot+PC.
2. Open the solution file MauiWindowsCopilotRuntimeSample.sln in Visual Studio 2022.
3. Ensure the debug toolbar has "Windows Machine" set as the target device.
4. Press F5 or select "Start Debugging" from the Debug menu to run the sample. Note: The sample can also be run without debugging by selecting "Start Without Debugging" from the Debug menu or Ctrl+F5.

## Integrate Phi Silica

With a local Phi Silica language model you can generate text responses to user prompts. First, ensure you have the pre-requisites and models available on your device as outlined in [Getting Started with Windows AI APIs](index.md).

### Using the right namespace
To use Phi Silica, make sure you are using the correct namespace:

### [C#](#tab/csharp0)
```csharp
using Microsoft.Windows.AI.Generative;
```

### [C++](#tab/cpp0)
```cpp
using namespace winrt::Microsoft::Windows::AI::Generative;
```
---

### Generate a complete response

This example shows how to generate a response to a Q&A prompt where the full response is generated before the result is returned.

1. Ensure the language model is available by calling the GetReadyState method and waiting for the **EnsureReadyAsync** method to return successfully.

1. Once the language model is available, create a **LanguageModel** object to reference it.

1. Submit a string prompt to the model using the **GenerateResponseAsync** method, which returns the complete result.

```csharp
using Microsoft.Windows.AI.Generative; 
 
 
if (!LanguageModel.GetReadyState()) 
{ 
   var op = await LanguageModel.EnsureReadyAsync(); 
} 
 
using LanguageModel languageModel = await LanguageModel.CreateAsync(); 
 
string prompt = "Provide the molecular formula for glucose."; 
 
var result = await languageModel.GenerateResponseAsync(prompt); 
 
Console.WriteLine(result.Response); 
```

```cpp
using namespace winrt::Microsoft::Windows::AI::Generative;

if (!LanguageModel::GetReadyState()) 
{
    auto op = LanguageModel::EnsureReadyAsync().get();
}

auto languageModel = LanguageModel::CreateAsync().get();

std::string prompt = "Provide the molecular formula for glucose.";

auto result = languageModel.GenerateResponseAsync(prompt).get();

std::cout << result.Response() << std::endl;
```

The response generated by this example is:

```output
C6H12O6
```

### Generate a complete response

Our API has built in content moderation which is customizable. This example shows how to specify your own thresholds for the internal content moderation. Learn more about [Content Moderation with Windows Copilot Runtime](./content-moderation.md).

1. Create a [`LanguageModel`](phi-silica-api-ref.md#languagemodel-class) object to reference the local language model. *A check has already been performed to ensure the Phi Silica language model is available on the user's device in the previous snippet.
1. Create a `ContentFilterOptions` object and specify your preferred values.
1. Submit a string prompt to the model using the [`GenerateResponseAsync`](phi-silica-api-ref.md#languagemodelgenerateresponseasyncsystemstring-method) method with the `ContentFilterOptions` as one of the parameters.

### [C#](#tab/csharp1)
```csharp
using LanguageModel languageModel = await LanguageModel.CreateAsync(); 
 
string prompt = "Provide the molecular formula for glucose."; 

ContentFilterOptions filterOptions = new ContentFilterOptions();
filterOptions.PromptMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;
filterOptions.ResponseMinSeverityLevelToBlock.ViolentContentSeverity = SeverityLevel.Medium;
 
var result = await languageModel.GenerateResponseAsync(null, prompt, filterOptions); 
 
Console.WriteLine(result.Response);
```

### [C++](#tab/cpp1)
```cpp
auto languageModel = LanguageModel::CreateAsync().get();

std::string prompt = "Provide the molecular formula for glucose.";

ContentFilterOptions contentFilter = ContentFilterOptions(); 
contentFilter.PromptMinSeverityLevelToBlock().ViolentContentSeverity(SeverityLevel::Medium); 
contentFilter.ResponseMinSeverityLevelToBlock().ViolentContentSeverity(SeverityLevel::Medium); 

// auto result = languageModel.GenerateResponseAsync(nullptr, prompt, filterOptions).get();

std::cout << result.Response() << std::endl;
```
---

### Generate a stream of partial responses

This example shows how to generate a response to a Q&A prompt where the response is returned as a stream of partial results.

1. Create a **LanguageModel** object to reference the local language model. *A check has already been performed to ensure the Phi Silica language model is available on the user's device in the previous snippet.

1. Asynchronously retrieve the **LanguageModelResponse** in a call to **GenerateResponseWithProgressAsync**. Write it to the console as the response is generated.

### [C#](#tab/csharp2)
```csharp
using LanguageModel languageModel = await LanguageModel.CreateAsync(); 
 
string prompt = "Introduce yourself."; 
 
AsyncOperationProgressHandler<LanguageModelResponse, string> 
progressHandler = (asyncInfo, delta) => 
{ 
    Console.WriteLine($"Progress: {delta}"); 
    Console.WriteLine($"Response so far: {asyncInfo.GetResults().Response}"); 
 }; 
 
var asyncOp = languageModel.GenerateResponseWithProgressAsync(prompt); 
 
asyncOp.Progress = progressHandler; 
 
var result = await asyncOp;  
 
Console.WriteLine(result.Response);
```

### [C++](#tab/cpp2)
```cpp
auto languageModel = LanguageModel::CreateAsync().get();

std::string prompt = "Introduce yourself.";

AsyncOperationProgressHandler<LanguageModelResponse, std::string> progressHandler = 
    [](const IAsyncOperationWithProgress<LanguageModelResponse, std::string>& asyncInfo, const std::string& delta) 
    { 
        std::cout << "Progress: " << delta << std::endl; 
        std::cout << "Response so far: " << asyncInfo.GetResults().Response << std::endl; 
    };

auto asyncOp = languageModel.GenerateResponseWithProgressAsync(prompt);

asyncOp.Progress(progressHandler); 

auto result = asyncOp.get();

std::cout << result.Response() << std::endl;
```
---

### Apply predefined text formats for more consistent responses in your app

Phi Silica includes the ability to predefine text response formats for use in your app. Predefining a text format can provide more consistent response results with the following options:

- **Text to Table**: Convert the prompt response into a table format.
- **Summarize**: Return a summary based on the prompt text.
- **Rewrite**: Rephrase the prompt text to add clarity and express the response in a more easily understood way.

1. Create a **LanguageModel** object to reference the local language model. *A check has already been performed to ensure the Phi Silica language model is available on the user's device in the previous snippet.

2. Create a **LanguageModelOptions** object and specify the predefined text format to use by assigning a **LanguageModelSkill** enum to the **Skill** field of the **LanguageModelOptions** object. The following values are available for the **LanguageModelSkill** enum.

    | Enum    | Description |
    | -------- | ------- |
    | **LanguageModelSkill.General** | Default value, no predefined formatting applied. |
    | **LanguageModelSkill.TextToTable** | Convert prompt text into a table if applicable. |
    | **LanguageModelSkill.Summarize**    | Return a summary based on the prompt text.  |
    | **LanguageModelSkill.Rewrite**  | Rewrite the prompt text response to improve clarity and comprehension.  |

3. Then we asynchronously retrieve the **LanguageModelResponse** in a call to **GenerateResponseWithProgressAsync** and write it to the console as the response is generated.

### [C#](#tab/csharp3)
```csharp
using Microsoft.Windows.AI.Generative; 
 
using LanguageModel languageModel = await LanguageModel.CreateAsync(); 
 
string prompt = "This is a large amount of text I want to have summarized.";

LanguageModelOptions options = new LanguageModelOptions {
    Skill = LanguageModelSkill.Summarize
};
 
var result = await languageModel.GenerateResponseAsync(options, prompt); 
 
Console.WriteLine(result.Response); 
```

### [C++](#tab/cpp3)
```cpp
using namespace winrt::Microsoft::Windows::AI::Generative;

auto languageModel = LanguageModel::CreateAsync().get();

std::string prompt = "This is a large amount of text I want to have summarized.";

LanguageModelOptions options = LanguageModelOptions();
options.Skill = LanguageModelSkill.Summarize;

auto result = languageModel.GenerateResponseAsync(options, prompt).get();

std::cout << result.Response() << std::endl;
```
---

## Responsible AI

Phi Silica provides developers with a powerful, trustworthy model for building apps with safe, secure AI experiences. The following steps have been taken to ensure Phi Silica is trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](/windows/ai/rai) when implementing AI features in your app.

- Thorough testing and evaluation of the model quality to identify and mitigate potential risks.
- Incremental roll out of Phi Silica experimental releases. Following the final Phi Silica experimental release, the roll out will expand to signed apps to ensure that malware scans have been applied to applications with local model capabilities.
- Phi Silica provides a localized AI model that includes a Text Content Moderation API. This API identifies and filters potentially harmful content in both the input and AI-generated output. The local text content moderation model is based on the [Azure AI Content Safety](https://azure.microsoft.com/products/ai-services/ai-content-safety) model for content moderation and provides similar performance. See [Text Content Moderation with Windows Copilot Runtime](./content-moderation.md) for a description of severity level filter options and a code sample demonstrating how to implement these options.

> [!IMPORTANT]
> No content safety system is infallible and occasional errors can occur, so we recommend integrating supplementary Responsible AI (RAI) tools and practices. For more details, see [Responsible Generative AI Development on Windows](/windows/ai/rai).

## Related content

- [Content Moderation with Windows Copilot Runtime](./content-moderation.md)
- [Access files and folders with Windows App SDK and WinRT APIs](/windows/apps/develop/files/winrt-files)
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for Phi Silica APIs in the Windows App SDK](phi-silica-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
