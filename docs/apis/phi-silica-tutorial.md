---
title: Get Started with AI imaging Walkthrough
description: Learn about the new Artificial Intelligence (AI) imaging features and walk through tutorials
ms.topic: article
ms.date: 04/14/2025
dev_langs:
- csharp
- cpp
---

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
```csharp
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
