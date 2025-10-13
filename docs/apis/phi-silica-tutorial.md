---
title: Get Started with a Phi Silica Walkthrough
description: Learn about the new Artificial Intelligence (AI) Phi Silica features and walk through tutorials
ms.topic: get-started
ms.date: 08/22/2025
dev_langs:
- csharp
- cpp
---

# Phi Silica walkthrough

> [!IMPORTANT]
> The Phi Silica APIs are part of a Limited Access Feature (see [LimitedAccessFeatures class](/uwp/api/windows.applicationmodel.limitedaccessfeatures)). For more information or to request an unlock token, please use the [LAF Access Token Request Form](https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409).

This short tutorial walks through the [Windows AI Foundry Sample for .NET MAUI](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry/cs-maui).

> [!NOTE]
> **Phi Silica features are not available in China.**

## Prerequisites

Complete the steps for .NET MAUI described in the [Get started building an app with Windows AI APIs](get-started.md).

## Introduction

This sample shows how to use various Windows AI APIs, including [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) for text generation and **ImageScaler** for scaling and sharpening images.

The sample includes the following four files:

1. MauiWindowsAISample.csproj: Adds the required Windows App SDK package reference for the Windows AI APIs and sets the necessary **TargetFramework** for Windows.
2. Platforms/Windows/MainPage.cs: Implements partial methods from the shared **MainPage** class that show and handle the text generation and image scaling functionality.
3. MainPage.xaml: Defines controls for showing text generation and image scaling.
4. MainPage.xaml.cs: Defines partial methods that MainPage.cs implements.

In the second file listed above, you'll find the following function, which demonstrates text summarization functionality.

1. Create a [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) instance (`languageModel`).
1. Pass that [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) to the [**TextSummarizer**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer) constructor.
1. Pass some text to the [**SummarizeAsync**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer.summarizeasync) method and print the result.

```csharp
using Microsoft.Windows.AI; 
 
using LanguageModel languageModel = await LanguageModel.CreateAsync(); 
 
string prompt = "This is a large amount of text I want to have summarized.";

LanguageModelOptions options = new LanguageModelOptions {
    Skill = LanguageModelSkill.Summarize
};
 
var result = await languageModel.GenerateResponseAsync(options, prompt); 
 
Console.WriteLine(result.Text); 
```

```cpp
using namespace winrt::Microsoft::Windows::AI::Generative;

auto languageModel = LanguageModel::CreateAsync().get();

std::string prompt = "This is a large amount of text I want to have summarized.";

LanguageModelOptions options = LanguageModelOptions();
options.Skill = LanguageModelSkill.Summarize;

auto result = languageModel.GenerateResponseAsync(options, prompt).get();

std::cout << result.Text() << std::endl;
```

## Build and run the sample

1. Clone the [WindowsAppSDK-Samples](https://github.com/microsoft/WindowsAppSDK-Samples) repo.
1. Switch to the "release/experimental" branch.
1. Navigate to the [Samples/WindowsAIFoundry/cs-maui](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry/cs-maui) folder.
1. Open MauiWindowsAISample.sln in Visual Studio 2022.
1. Ensure the debug toolbar has "Windows Machine" set as the target device.
1. Press F5 or select "Start Debugging" from the Debug menu to run the sample (the sample can also be run without debugging by selecting "Start Without Debugging" from the Debug menu or Ctrl+F5).
1. Click one of the "Scale" buttons to scale the image, or enter a text prompt and click the "Generate" button to generate a text response.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
