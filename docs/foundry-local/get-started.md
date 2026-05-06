---
title: Get started with Foundry Local
description: Build a C# or .NET app that runs large language models locally on Windows using the Microsoft.AI.Foundry.Local.WinML NuGet package.
ms.date: 04/28/2026
ms.topic: get-started
dev_langs:
- csharp
---

# Get started with Foundry Local

Foundry Local enables local execution of large language models (LLMs) directly on your Windows device, as part of [Microsoft Foundry on Windows](../overview.md). It's a good alternative when you need to go deeper than the Windows AI APIs, or need to support hardware that isn't a Copilot+ PC. No special permissions or unlock tokens are required — it runs entirely on your own hardware. The same pattern works in a console app, a WinUI 3 app, a WPF app, or any other .NET host.

:::image type="content" source="../images/tech-logos.png" alt-text="Logos of technologies associated with Foundry Local":::

> [!NOTE]
> Full documentation for Foundry Local — including the CLI, model management, the REST API, Python SDK, and more — is maintained in the **Azure AI Foundry documentation**. Links in this page take you there when needed. Use your browser's back button or the breadcrumb to return to the Windows AI docs at any time.
>
> If you're not sure whether Foundry Local is the right choice for your scenario, see [Choose your Windows AI solution](../windows-ai-comparison.md) before continuing.

## Prerequisites

- Windows 10 build 26100 or later (Windows 11 24H2 or later recommended)
- [.NET 9.0 SDK](https://dotnet.microsoft.com/download/dotnet/9.0) or later
- A DirectX 12–capable GPU (integrated or discrete). The `WinML` package uses hardware acceleration and requires real GPU hardware — virtual machines without GPU passthrough are not supported.

### Install the Foundry Local CLI

Install the CLI using winget:

```powershell
winget install Microsoft.FoundryLocal
```

Then close and reopen your terminal so the `foundry` command is on your PATH. Verify:

```bash
foundry --version
```


## Create a project

```bash
dotnet new console -n FoundryLocalDemo
cd FoundryLocalDemo
```

The NuGet package includes native Windows binaries, so the project needs a Windows target framework and runtime identifiers. Open `FoundryLocalDemo.csproj` and replace the `<PropertyGroup>` block with:

```xml
<PropertyGroup>
  <OutputType>Exe</OutputType>
  <TargetFramework>net9.0-windows10.0.26100.0</TargetFramework>
  <Nullable>enable</Nullable>
  <ImplicitUsings>enable</ImplicitUsings>
  <RuntimeIdentifiers>win-x64;win-arm64</RuntimeIdentifiers>
</PropertyGroup>
```

Then restore to generate the assets file for the new target:

```bash
dotnet restore
```

## Install the NuGet package

Install the WinML package, which automatically uses the best available hardware (Qualcomm NPU, NVIDIA GPU, or CPU) via ONNX Runtime:

```bash
dotnet add package Microsoft.AI.Foundry.Local.WinML --version 1.0.0
dotnet add package Betalgo.Ranul.OpenAI --version 9.1.0
```

The `Betalgo.Ranul.OpenAI` package provides the `ChatMessage` and related types used by the Foundry Local chat API.

> [!NOTE]
> If you need to target non-Windows platforms, use `Microsoft.AI.Foundry.Local` instead. The API is identical; that package omits the Windows-specific hardware acceleration.

## Quick-start: run a model

Replace the contents of `Program.cs` with the following, then run `dotnet run`. The program initializes Foundry Local, downloads the model if needed, runs a chat completion, and cleans up.

```csharp
using Microsoft.AI.Foundry.Local;
using Microsoft.Extensions.Logging.Abstractions;
using Betalgo.Ranul.OpenAI.ObjectModels.RequestModels;

// 1. Initialize Foundry Local. The SDK starts the service automatically if needed.
await FoundryLocalManager.CreateAsync(
    new Configuration { AppName = "my-app" },
    NullLogger.Instance);

var manager = FoundryLocalManager.Instance;
try
{
    // 2. Look up the model in the catalog by alias.
    var catalog = await manager.GetCatalogAsync();
    var model = await catalog.GetModelAsync("phi-3.5-mini")
        ?? throw new Exception(
            "Model 'phi-3.5-mini' not found in catalog. " +
            "Ensure Foundry Local is installed and has internet access.");

    // 3. Download the model if it is not already cached (2.53 GB).
    if (!await model.IsCachedAsync())
    {
        Console.Write("Downloading phi-3.5-mini...");
        await model.DownloadAsync(progress =>
        {
            Console.Write($"\rDownloading phi-3.5-mini  {progress,5:F1}%");
        });
        Console.WriteLine();
    }

    // 4. Load the model into memory.
    await model.LoadAsync();

    // 5. Run a chat completion.
    var chatClient = await model.GetChatClientAsync();
    var response = await chatClient.CompleteChatAsync(new[]
    {
        new ChatMessage { Role = "system", Content = "You are a helpful assistant." },
        new ChatMessage { Role = "user", Content = "Explain async/await in C# in two sentences." }
    });

    if (!response.Successful)
        throw new Exception(
            $"Chat completion failed: {response.Error?.Message ?? "unknown error"} " +
            $"(code: {response.Error?.Code})");

    var content = response.Choices![0].Message.Content;
    if (string.IsNullOrEmpty(content))
        throw new Exception(
            "Model returned empty content. " +
            "Verify that your device has a DirectX 12-capable GPU. " +
            "Virtual machines without GPU passthrough are not supported.");

    Console.WriteLine(content);
}
finally
{
    // 6. Clean up — always runs even if an earlier step throws.
    manager.Dispose();
}
```

### Streaming responses

For a better user experience in UI apps, stream the response token-by-token.
This snippet continues from the quick-start above — `chatClient` comes from step 5:

```csharp
using var cts = new CancellationTokenSource();

await foreach (var chunk in chatClient.CompleteChatStreamingAsync(
    new[] { new ChatMessage { Role = "user", Content = "Write a haiku about Windows." } },
    cts.Token))
{
    Console.Write(chunk.Choices?[0]?.Message?.Content);
}
Console.WriteLine();
```

### Tune generation parameters

```csharp
chatClient.Settings.Temperature = 0.7f;
chatClient.Settings.MaxTokens = 512;
chatClient.Settings.TopP = 0.9f;
```

## Model aliases

Pass a **model alias** (not a full model ID) to `GetModelAsync` so that Foundry Local automatically selects the best hardware variant — for example, a QNN NPU variant on Snapdragon, a CUDA variant on NVIDIA, or a CPU fallback everywhere else.

Run the CLI to see available aliases:

```bash
foundry model list
```

Common aliases: `phi-3.5-mini`, `phi-4`, `qwen2.5-7b`, `deepseek-r1-7b`. The full catalog is at [foundrylocal.ai/models](https://www.foundrylocal.ai/models).

## Use from a WinUI 3 or WPF app

Initialize once in `App.xaml.cs` or `App.cs`:

```csharp
protected override async void OnLaunched(Microsoft.UI.Xaml.LaunchActivatedEventArgs args)
{
    await FoundryLocalManager.CreateAsync(
        new Configuration { AppName = "MyWinUIApp" },
        NullLogger.Instance);
    // ...
}
```

Then resolve `FoundryLocalManager.Instance` anywhere in the app. Call `Dispose()` in the app's exit handler.

## Fallback to cloud

Combine Foundry Local with [Windows AI APIs](../apis/get-started.md) and Azure OpenAI for a resilient multi-tier pattern. See [Choose your Windows AI solution](../windows-ai-comparison.md#combine-options-in-the-same-app) for a complete compilable example.

## Troubleshooting

**`OGA Error: N instances of struct Generators::Model were leaked`**  
These warnings appear after the program exits and are benign. They come from the underlying ONNX Runtime GenAI (OGA) library's native resource tracking. Your output is correct; the warnings don't indicate a problem with your code.

**`Error in cpuinfo: Unknown chip model name 'Snapdragon...'`**  
This warning from ONNX Runtime means the library doesn't recognize your ARM SoC for CPU feature detection. It falls back to safe defaults and inference runs normally. No action needed.

**`Model '...' not found in catalog`**  
The SDK fetches the model catalog from the internet. Check your network connection. If a specific model alias isn't found, run `foundry model list` to see available aliases, or browse the full catalog at [foundrylocal.ai/models](https://www.foundrylocal.ai/models).

**Model returns empty content**  
The WinML backend requires a DirectX 12-capable GPU. Virtual machines without GPU passthrough return a successful response with empty content. Run on physical hardware with a discrete or integrated GPU.



- [Full Foundry Local documentation](/azure/ai-foundry/foundry-local/) — CLI, REST API, Python SDK, model management
- [Foundry Local C# SDK reference](/azure/ai-foundry/foundry-local/reference/csharp-sdk) — complete API reference
- [Windows ML](../new-windows-ml/overview.md) — bring your own ONNX model with full EP control
- [Choose your Windows AI solution](../windows-ai-comparison.md) — compare all Windows AI options

