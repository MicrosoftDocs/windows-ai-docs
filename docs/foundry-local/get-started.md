---
title: Get started with Foundry Local
description: Build a C# or .NET app that runs large language models locally on Windows using the Microsoft.AI.Foundry.Local.WinML NuGet package.
ms.date: 09/21/2026
ms.topic: get-started
dev_langs:
- csharp
---

# Get started with Foundry Local

Foundry Local enables local execution of large language models (LLMs) directly on your Windows device, as part of [Microsoft Foundry on Windows](../overview.md). It's a good alternative when you need to go deeper than the Windows AI APIs, or need to support hardware that isn't a Copilot+ PC. No special permissions or unlock tokens are required. The native SDK runs in your app process and doesn't require the Foundry Local CLI or a separate local REST server. The same pattern works in a console app, a WinUI 3 app, a WPF app, or any other .NET host.

:::image type="content" source="../images/tech-logos.png" alt-text="Logos of technologies associated with Foundry Local":::

> [!NOTE]
> Full documentation for Foundry Local — including the CLI, model management, the optional REST server, Python SDK, and more — is maintained in the **Microsoft Foundry documentation**. Links in this page take you there when needed. Use your browser's back button or the breadcrumb to return to the Windows AI docs at any time.
>
> If you're not sure whether Foundry Local is the right choice for your scenario, see [Choose your Windows AI solution](../windows-ai-comparison.md) before continuing.

## Prerequisites

- Windows 11, version 24H2 (build 26100) or later
- [.NET 9.0 SDK](https://dotnet.microsoft.com/download/dotnet/9.0) or later
- An x64 or Arm64 device with enough memory and disk space for the model you select
- Internet access for the initial package, model, and runtime-component downloads

The Windows SDK can use compatible CPU, GPU, and NPU model variants. A dedicated GPU, NPU, or Copilot+ PC isn't required when the selected model has a compatible CPU variant. Available acceleration and performance depend on your device, model, and execution provider.

> [!NOTE]
> This quickstart uses **Phi-4 Mini**, currently the latest Microsoft Phi model available through the Foundry Local alias `phi-4-mini`. Foundry Local catalog aliases and recommended models can change as the catalog evolves.
>
> Phi-4 Mini is separate from **Phi Silica**, the Windows AI API model that ships with Windows. Phi Silica remains a Limited Access Feature and requires an unlock token. It is scheduled to be replaced by Aion Instruct, which won't require a Limited Access Feature token. See [Get started with Phi Silica](../apis/phi-silica.md) for access details and the transition timeline.

### Optional: Install the Foundry Local CLI

The SDK workflow in this quickstart doesn't require the CLI. Install it only if you also want to inspect and manage models from a terminal:

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

Install the current stable Windows package:

```bash
dotnet add package Microsoft.AI.Foundry.Local.WinML
```

The package includes the `ChatMessage` and related types used by the Foundry Local native chat API. It selects a compatible model variant for the current device and can use Windows ML execution providers for hardware acceleration.

> [!NOTE]
> If you need to target non-Windows platforms, use `Microsoft.AI.Foundry.Local` instead. It provides the same Foundry Local API surface without the Windows ML integration.
>
> The command above installs the current stable package. The [WinUI tutorial](tutorials/winui-local-ai/intro.md) uses .NET 10 and pins package versions so that you can reproduce its complete walkthrough.

## Quick-start: run a model

Replace the contents of `Program.cs` with the following, then run `dotnet run`. The program initializes Foundry Local, downloads the model if needed, runs a chat completion, and cleans up.

```csharp
using Microsoft.AI.Foundry.Local;
using Microsoft.Extensions.Logging.Abstractions;
using Betalgo.Ranul.OpenAI.ObjectModels.RequestModels;

// 1. Initialize the native in-process Foundry Local SDK.
await FoundryLocalManager.CreateAsync(
    new Configuration { AppName = "my-app" },
    NullLogger.Instance);

var manager = FoundryLocalManager.Instance;
try
{
    // 2. Look up the model in the catalog by alias.
    var catalog = await manager.GetCatalogAsync();
    var model = await catalog.GetModelAsync("phi-4-mini")
        ?? throw new Exception(
            "Model 'phi-4-mini' not found in catalog. " +
            "Check your internet connection and available model aliases.");

    // 3. Download the model if it is not already cached.
    if (!await model.IsCachedAsync())
    {
        Console.Write("Downloading phi-4-mini...");
        await model.DownloadAsync(progress =>
        {
            Console.Write($"\rDownloading phi-4-mini  {progress,5:F1}%");
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
            "Try the request again or select another compatible model variant.");

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

Pass a **model alias** (not a full model ID) to `GetModelAsync` so that Foundry Local can select a compatible hardware variant. Depending on the model and device, this can be a QNN NPU variant on Snapdragon, a CUDA variant on NVIDIA, or a CPU variant.

If you installed the optional CLI, run it to see available aliases:

```bash
foundry model list
```

For example, use `phi-4-mini` for the Microsoft Phi-4 Mini model. The catalog changes over time, so check the [Foundry Local model catalog](https://www.foundrylocal.ai/models) for the current aliases and available variants.

## Python quick-start

Foundry Local also supports Python, JavaScript (Node.js), and Rust. Here's the minimal Python example to confirm the pattern works — the full walkthrough for all four languages is in the [Microsoft Foundry documentation](/azure/foundry-local/get-started).

Install **one** of the following — do not install both, as they have conflicting `onnxruntime-core` dependencies:

```bash
pip install foundry-local-sdk-winml   # Windows — includes hardware acceleration (recommended on Windows)
pip install foundry-local-sdk         # macOS/Linux, or Windows without hardware acceleration
```

> [!IMPORTANT]
> The `foundry-local` package on PyPI (without `-sdk`) is an unrelated third-party package. Install `foundry-local-sdk` or `foundry-local-sdk-winml` to get the Microsoft Foundry Local SDK.

Create `app.py`:

```python
from foundry_local_sdk import Configuration, FoundryLocalManager

FoundryLocalManager.initialize(Configuration(app_name="my-app"))
manager = FoundryLocalManager.instance

model = manager.catalog.get_model("phi-4-mini")
model.download(lambda p: print(f"\rDownloading {p:.0f}%", end="", flush=True))
model.load()

client = model.get_chat_client()
for chunk in client.complete_streaming_chat([{"role": "user", "content": "Why is the sky blue?"}]):
    print(chunk.choices[0].delta.content or "", end="", flush=True)
print()

model.unload()
```

Run it:

```bash
python app.py
```

For the complete Python quick-start — including execution provider setup, error handling, and model listing — see [Get started with Foundry Local](/azure/foundry-local/get-started) in the Microsoft Foundry documentation.

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

For a complete application walkthrough, continue to the WinUI tutorial. It covers explicit model-download consent, progress, cancellation, accessibility, and output review.

> [!div class="nextstepaction"]
> [Add local AI to a WinUI app](tutorials/winui-local-ai/intro.md)

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
Try the request again and confirm that the selected model has a compatible variant for your device. If the problem continues, select a smaller model or a CPU variant and check that the device has enough available memory.

**`foundry-local-sdk-winml requires onnxruntime-core==X.Y.Z, but you have ... which is incompatible`**  
This pip dependency conflict means both `foundry-local-sdk-winml` and `foundry-local-sdk` are installed — they pin different versions of `onnxruntime-core` and cannot coexist. Uninstall one:

```bash
pip uninstall foundry-local-sdk        # if you want the winml (Windows) package
pip uninstall foundry-local-sdk-winml  # if you want the cross-platform package
```

Then reinstall the one you want. Using a [virtual environment](https://docs.python.org/3/library/venv.html) avoids this problem entirely.



- [Full Foundry Local documentation](/azure/foundry-local/) — CLI, REST API, Python SDK, model management
- [Foundry Local SDK reference](/azure/foundry-local/reference/reference-sdk-current) — SDK setup and API guidance
- [Windows ML](../new-windows-ml/overview.md) — bring your own ONNX model with full EP control
- [Choose your Windows AI solution](../windows-ai-comparison.md) — compare all Windows AI options
