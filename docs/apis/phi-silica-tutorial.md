---
title: Get Started with a Phi Silica Walkthrough
description: Learn about the new Artificial Intelligence (AI) Phi Silica features and walk through tutorials
ms.topic: article
ms.date: 04/24/2025
dev_langs:
- csharp
- cpp
---

# Phi Silica walkthrough

![Screenshot of the home page for the Windows AI Foundry Sample app.](../images/API-Tutorial-MAUIappimage1(initialized).png)

This short tutorial walks through the [Windows AI Foundry Sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsCopilotRuntime/cs-maui) for .NET MAUI.

## Prerequisites

Complete the steps in the [Getting Started page](get-started.md) for .NET MAUI.

## Introduction

This sample shows how to use various Windows AI APIs, including **LanguageModel** for text generation and **ImageScaler** for scaling and sharpening images.

The sample includes the following four files:

1. MauiWindowsCopilotRuntimeSample.csproj: Adds the required Windows App SDK package reference for the Windows AI APIs and sets the necessary **TargetFramework** for Windows.
2. Platforms/Windows/MainPage.cs: Implements partial methods from the shared **MainPage** class that show and handle the text generation and image scaling functionality.
3. MainPage.xaml: Defines controls for showing text generation and image scaling.
4. MainPage.xaml.cs: Defines partial methods that MainPage.cs implements.

In the second file listed above, you'll find the following function, which demonstrates some basic functionality for the **LanguageModel** method.

```csharp
using Microsoft.Windows.AI.Generative; 
 
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

![The sample app after calling the ImageScaler and LanguageModel APIs.](../images/API-Tutorial-MAUIappimage2(aftercallingSuperResandLanguageModelAPIs).png)

## Build and run the sample

1. Clone the [repository](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsCopilotRuntime/cs-maui) onto your Copilot+ PC.
1. Open the solution file MauiWindowsCopilotRuntimeSample.sln in Visual Studio 2022.
1. Ensure the debug toolbar has "Windows Machine" set as the target device.
1. Press F5 or select "Start Debugging" from the Debug menu to run the sample. (The sample can also be run without debugging by selecting "Start Without Debugging" from the Debug menu or Ctrl+F5.)
1. Click one of the "Scale" buttons to scale the image, or enter a text prompt and click the "Generate" button to generate a text response.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI Foundry Sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsCopilotRuntime)
