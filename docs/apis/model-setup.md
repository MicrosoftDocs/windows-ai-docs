---
title: Set up your development environment to build Windows Copilot Runtime APIs
description: Learn how to set up your development environment to build Windows Copilot Runtime APIs and check for model availability.
ms.topic: article
ms.date: 02/06/2025
ms.author: mattwoj
author: mattwojo
reviewer: raamleka
dev_langs:
- csharp
- cpp
---

# Set up your development environment to build Windows Copilot Runtime APIs

To use Windows Copilot Runtime APIs, you will first need to ensure that your Copilot+ PC is set up correctly. The following guidance will walk through each step of setting up your development environment.

## Prerequisites

- A [Copilot+ PC](/windows/ai/npu-devices/) containing a Qualcomm Snapdragon&reg; X processor.
  - Arm64EC (Emulation Compatible) is not currently supported.
- [Windows 11 Insider Preview Build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/) or later must be installed on your device.
- [.NET 8 SDK (or higher) installed](https://dotnet.microsoft.com/download)
- (Optional) [Windows SDK installed (10.0.26100 - do verify min supported version for WCR)](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/). The Windows SDK is typically installed with Windows App Development workloads in the Visual Studio installer. Otherwise, you can install manually from link above.

## Build and run a Windows Copilot Runtime sample app

You can find samples on GitHub in the WindowsAppSDK-Samples repo: [WindowsAppSDK-Samples/Samples
/WindowsCopilotRuntime/](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsCopilotRuntime). This WinUI sample app will demonstrate how to use Windows Copilot Runtime APIs.

1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/).
2. Ensure your build configuration is set to `arm64`.
3. Open the solution file (.sln) in Visual Studio and select **Run** (Start Debugging F5, or Start without debugging Ctrl+F5). This will launch the Windows Copilot Runtime sample with default configs - packaged, framework-dependent app pointing to `winappsdk1.7-experimental3` nuget. It will install the runtime if needed, during app deployment.

## Build and run a unpackaged WinUI Console app with Windows Copilot Runtime APIs

If you prefer to build an **unpackaged WinUI / Console app** that utilizes Windows Copilot Runtime APIs, see the following guidance.

1. Ensure that your build configuration is set to `arm64`.
2. Add [`winappsdk1.7-experimental3` nuget package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK/1.7.250127003-experimental3) from the nuget store. If you cannot see it, enable **Include prerelease** in nuget store.
3. Edit your project file (.csproj) to ensure that the target framework is set to the following:

 <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>

If you started with WinUI 3 packaged app template, this is all you need. You can start building and running your app.

See the Windows App SDK Experimental channel release notes for [
Version 1.7 Experimental (1.7.0-experimental3)](/windows/apps/windows-app-sdk/experimental-channel#version-17-experimental-170-experimental3).

If you started with a **C# console app** or want to have an **unpackaged app**, you will need to add `<WindowsPackageType> None </WindowsPackageType>` in your project file to declare it as unpackaged.

For unpackaged apps, you will also need to [install Windows app runtime](/windows/apps/windows-app-sdk/downloads#experimental-release). Making sure to select the `arm64` installer.

## Troubleshooting

If things are not working, first try running the [Windows Copilot Runtime sample app](#build-and-run-a-windows-copilot-runtime-sample-app) to see if it works correctly.

If this fails, [ensure you have models installed on your machine. That can be verified by going to **System > AI Components** in Settings app and see entries for different AI models. If not, then you may not be on right branch or something else.

## Check for model availability before implementing an AI feature

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

When implementing an AI feature using Windows Copilot Runtime APIs, the app should first check for the availability of the AI model supporting that feature. Unlike typical Windows App SDK APIs where a developer can call on an API to immediately provide functionality or content, the Windows Copilot Runtime APIs rely on the model to be available on the app users machine.

## Checking Model Availability 

To check if the model required by an AI feature is available on the user's device, begin by calling: `IsAvailable()`. This method will return `true` if the model being called is installed on the user's device. This method needs to be called before every call to the model.

If the model is not available on the user's device, the method `MakeAvailableAsync()` can be called to install the required model. The model installation will run in the background, and the user will be able to check on the install progress in the Windows Update page of the Settings application.

The `MakeAvailableAsync()` method has a status option which can show a loading UI. If the user has unsupported hardware, `MakeAvailableAsync()` will fail with an error.

Once the model is available, `CreateAsync()` can be called to create a new instance from a class that belongs to the model. The APIs that belong to that class can then be used in the app.

## Code sample

The following sample demonstrates checking for model availability.

```csharp
using Microsoft.Windows.AI.Generative; 
 
 
if (!LanguageModel.IsAvailable()) 
{ 
   var op = await LanguageModel.MakeAvailableAsync(); 
} 
 
using LanguageModel languageModel = await LanguageModel.CreateAsync();

string prompt = "Provide the molecular formula for glucose.";  
var result = await languageModel.GenerateResponseAsync(prompt); 
 
Console.WriteLine(result.Response); 
```

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
