---
title: Get started with Phi3 and other language models in your Windows app with OnnxRuntime Generative AI 
description: TODO Description needed.
ms.author: drewbat
author: drewbatgit
ms.date: 05/21/2024
ms.topic: article
#customer intent: As a <role>, I want <what> so that <why>.
---

# Get started with Phi3 and other language models in your Windows app with OnnxRuntime Generative AI

This article walks you through creating a WinUI 3 app that uses a Phi3 model and the [ONNX Runtime Generative AI - TBD link target] library to implement a simple generative AI chat app.


## What is ONNX Runtime Generative AI

[TBD]


## Prerequisites

- Your device must have developer mode enabled. For more information see [Enable your device for development](/windows/apps/get-started/enable-your-device-for-development).
- Visual Studio 2022 or later with the [TBD - required workloads / components] workload.

## Create a new C# WinUI app

In Visual Studio, create a new project. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "winui", then select the **Black app, Packaged (WinUI3 in Desktop)** template. Name the new project "GenAIExample".

## Add references to the ONNX Runtime Generative AI Nuget package

In **Solution Explorer**, right-click **Dependencies** and select **Manage NuGet packages...**. In the NuGet package manager, select the **Browse** tab. Search for "Microsoft.ML.OnnxRuntimeGenAI.DirectML", select the latest stable version in the **Version** drop-down and then click **Install**.


## Add a model and vocabulary file to your project

In **Solution Explorer**, right-click your project and select **Add->New Folder**. Name the new folder "model". For this example, we will be using the model from [https://huggingface.co/microsoft/Phi-3-mini-4k-instruct-onnx/tree/main/directml/directml-int4-awq-block-128](https://huggingface.co/microsoft/Phi-3-mini-4k-instruct-onnx/tree/main/directml/directml-int4-awq-block-128).

There are several different ways to retrieve models. For this walkthrough, we will use the Hugging Face Command Line Interface (CLI). If you get the models using another method, you may have to adjust the file paths to the model in the example code. For information on installing the Hugging Face CLI and setting up your account to use it, see [Command Line Interface (CLI)](https://huggingface.co/docs/huggingface_hub/main/en/guides/cli).

After installing the CLI, open a terminal, navigate to the Models directory you created, and type the following command.

`huggingface-cli download microsoft/Phi-3-mini-4k-instruct-onnx --include directml/* --local-dir .`

When the operation is complete verify that the following file exists: `[Project Directory]\Models\directml\directml-int4-awq-block-128\model.onnx`.

In **Solution Explorer**, expand the "directml-int4-awq-block-128" folder and select all of the files in the folder. In the **File Properties** pane, set **Copy to Output Directory** to "Copy if newer".

## Add a simple UI to interact with the model

For this example we will create a very simplistic UI that has a **TextBox** for specifying a prompt, a **Button** for submitting the prompt, and a **TextBlock** for displaying status messages and the responses from the model. Replace the default **StackPanel** element in `MainWindow.xaml` with the following XAML.

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <StackPanel Orientation="Vertical" HorizontalAlignment="Center" VerticalAlignment="Center" Grid.Column ="0">
        <TextBox x:Name="promptTextBox" Text="Compose a haiku about coding."/>
        <Button x:Name="myButton" Click="myButton_Click">Submit prompt</Button>
    </StackPanel>
    <Border Grid.Column="1" Margin="20">
        <TextBlock x:Name="responseTextBlock" TextWrapping="WrapWholeWords"/>
    </Border>
</Grid>
```

## Initialize the model

In `MainWindow.xaml.cs`, add a using directive for the **Microsoft.ML.OnnxRuntimeGenAI** namespace. [TBD - Where is the API reference documentation for these APIs so that I can link to individual APIs?]

```csharp
using Microsoft.ML.OnnxRuntimeGenAI;
```

Declare member variables inside the **MainPage** class definition for the **Model** and the **Tokenizer**. Set the location for the model files we added in the previous steps.

```csharp
private Model? model = null;
private Tokenizer? tokenizer = null;
private readonly string ModelDir = 
    Path.Combine(AppDomain.CurrentDomain.BaseDirectory,
        @"Models\directml\directml-int4-awq-block-128");
```

Create a helper method to asynchronously initialize the model. This method calls the constructor for the **Model** class, passing in the path to the model directory. Next it creates a new **Tokenizer** from the model.

```csharp
public Task InitializeModelAsync()
{

    DispatcherQueue.TryEnqueue(() =>
    {
        responseTextBlock.Text = "Loading model...";
    });

    return Task.Run(() =>
    {
        var sw = Stopwatch.StartNew();
        model = new Model(ModelDir);
        tokenizer = new Tokenizer(model);
        sw.Stop();
        Debug.WriteLine($"Model loading took {sw.ElapsedMilliseconds} ms");
        DispatcherQueue.TryEnqueue(() =>
        {
            responseTextBlock.Text = $"Model loading took {sw.ElapsedMilliseconds} ms";
        });
    });
}
```

For this example, we will load the model when the main window is activated. Update the page constructor to register a handler for the [Activated](/windows/windows-app-sdk/api/winrt/microsoft.ui.xaml.window.activated) event.

```csharp
public MainWindow()
{
    this.InitializeComponent();
    this.Activated += MainWindow_Activated;
}
```

The **Activated** event can be raised multiple times, so in the event handler, check to make sure the model is null before initializing it.

```csharp
private async void MainWindow_Activated(object sender, WindowActivatedEventArgs args)
{
    if (model == null)
    {
        await InitializeModelAsync();
    }
}
```


## Submit the prompt to the model

Create a helper method that submits the prompt to the model and then asynchronously returns the results to the caller with an [IAsyncEnumerable](/dotnet/api/system.collections.generic.iasyncenumerable-1). 

In this method, the [Generator](https://onnxruntime.ai/docs/genai/api/csharp.html#generator-class) class is used in a loop, calling **GenerateNextToken** in each pass to retrieve what the model predicts the next few characters, called a token, should be based on the input prompt. The loop runs until the generator **IsDone** method returns true or until any of the tokens "<|end|>", "<|system|>", or "<|user|>" are received, which signals that we can stop generating tokens.

```csharp
public async IAsyncEnumerable<string> InferStreaming(string prompt)
{
    if (model == null || tokenizer == null)
    {
        throw new InvalidOperationException("Model is not ready");
    }

    var generatorParams = new GeneratorParams(model);

    var sequences = tokenizer.Encode(prompt);

    generatorParams.SetSearchOption("max_length", 2048);
    generatorParams.SetInputSequences(sequences);
    generatorParams.TryGraphCaptureWithMaxBatchSize(1);

    using var tokenizerStream = tokenizer.CreateStream();
    using var generator = new Generator(model, generatorParams);
    StringBuilder stringBuilder = new();
    while (!generator.IsDone())
    {
        string part;
        try
        {
            await Task.Delay(10).ConfigureAwait(false);
            generator.ComputeLogits();
            generator.GenerateNextToken();
            part = tokenizerStream.Decode(generator.GetSequence(0)[^1]);
            stringBuilder.Append(part);
            if (stringBuilder.ToString().Contains("<|end|>")
                || stringBuilder.ToString().Contains("<|user|>")
                || stringBuilder.ToString().Contains("<|system|>"))
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex);
            break;
        }

        yield return part;
    }
}
```

## Add UI code to submit the prompt and display the results

In the **Button** click handler, first verify that the model is not null. Create a prompt string with the system and user prompt and call **InferStreaming**, updating the **TextBlock** with each part of the response. 

The model used in this example has been trained to accept prompts in the following format, where `systemPrompt` is the instructions for how the model should behave, and `userPrompt` is the question from the user.

`<|system|>{systemPrompt}<|end|><|user|>{userPrompt}<|end|><|assistant|>`

Models should document their prompt conventions. For this model the format is documented on the [Huggingface model card](https://huggingface.co/microsoft/Phi-3-mini-4k-instruct-onnx).

```csharp
private async void myButton_Click(object sender, RoutedEventArgs e)
{
    responseTextBlock.Text = "";

    if(model != null)
    {
        var systemPrompt = "You are a helpful assistant.";
        var userPrompt = promptTextBox.Text;

        var prompt = $@"<|system|>{systemPrompt}<|end|><|user|>{userPrompt}<|end|><|assistant|>";
        
        await foreach (var part in InferStreaming(prompt))
        {
            responseTextBlock.Text += part;
        }
    }
}
```

## Run the example

Build and run the project. Wait for the **TextBlock** to indicate that the model has been loaded. Type a prompt into the prompt text box and click the submit button. You should see the results gradually populate the text block.

## Next steps

[TBD - Next steps]

## See also

[TBD - See also]
