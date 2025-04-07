---
title: Set up your development environment to build Windows Copilot Runtime APIs
description: Learn how to set up your development environment to build Windows Copilot Runtime APIs and check for model availability.
ms.topic: article
ms.date: 02/19/2025
ms.author: mattwoj
author: mattwojo
reviewer: raamleka
dev_langs:
- csharp
- cpp
---

# Set up your development environment to use Windows Copilot Runtime APIs

To use Windows Copilot Runtime APIs, you will first need to ensure that your Copilot+ PC is set up correctly. The following guidance will walk through each step of setting up your development environment.

For release notes on this Windows App SDK Experimental release, see [Version 1.7 Experimental (1.7.0-experimental3)](/windows/apps/windows-app-sdk/experimental-channel#version-17-experimental-170-experimental3).

Find guidance on how to [Check for model availability](#3-check-for-model-availability) below as well.

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

## Prerequisites

- A [Copilot+ PC](/windows/ai/npu-devices/) containing a Qualcomm Snapdragon&reg; X processor.
  - Arm64EC (Emulation Compatible) is not currently supported.
- [Windows 11 Insider Preview Build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/) or later must be installed on your device.
- [.NET 8 SDK (or higher) installed](https://dotnet.microsoft.com/download)
- (Optional) [Windows SDK installed (10.0.26100 - do verify min supported version for WCR)](https://developer.microsoft.com/windows/downloads/windows-sdk/). The Windows SDK is typically installed with Windows App Development workloads in the Visual Studio installer. Otherwise, you can install manually from link above.

## 1. Check if your PC is correctly configured

The simplest way to verify that your PC is correctly setup to use the Windows Copilot Runtime APIs, is to use the WCR API tab in the AI Dev Gallery.
1. Download [AI Dev Gallery](https://apps.microsoft.com/detail/9N9PN1MM3BD5) or clone the project from [GitHub](https://github.com/microsoft/ai-dev-gallery) (ensure your build configuration is set to `ARM64` in Visual Studio).
2. Open AI Dev Gallery, navigate to the WCR API tab and select Phi Silica.
3. The sample should run straight away if the model is already available on your device, or allows you to download the model first.

## 2. Build an app with Windows Copilot Runtime APIs

If you prefer to build your own app that utilizes Windows Copilot Runtime APIs, select your preferred framework below:

### [WinUI](#tab/winui)
1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/)
2. Create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.
3. Right-click your project file (.csproj), and ensure that the target framework is set to target 22621 or later:

```xml
 <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
```
4. Add the [`winappsdk1.7-experimental3` NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK/1.7.250127003-experimental3): right-click on your project, and select 'Manage NuGet Packages..'. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`.
5. Ensure that your build configuration is set to `ARM64`.
6. Build and run your app.

### [WinForms](#tab/winforms)
1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/).
2. Create a new WinForms project by selecting the **Windows Forms App** template.

To configure your WinForms project for [Windows App SDK support](/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/winforms-plus-winappsdk#configure-your-winforms-project-for-windows-app-sdk-support):

3. Set **TargetFramework** inside the **PropertyGroup** to:

    ```xml
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    ```

4. Add a [**RuntimeIdentifiers**](/dotnet/core/project-sdk/msbuild-props#runtimeidentifiers) element inside the **PropertyGroup**:

    ```xml
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    ```

5. By default, a WinForms app is unpackaged (meaning that it isn't installed by using MSIX). An unpackaged app must initialize the Windows App SDK runtime before using any other feature of the Windows App SDK. You can do that automatically when your app starts via auto-initialization. Inside the **PropertyGroup** element, set the **WindowsPackageType** project property to:

    ```xml
    <WindowsPackageType>None</WindowsPackageType>
    ```

To learn more, see [Use the Windows App SDK in a Windows Forms (WinForms) app](/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/winforms-plus-winappsdk).

6. Add the [`winappsdk1.7-experimental3` NuGet package](https://www.nuget.org/packages/Microsoft.WindowsAppSDK/1.7.250127003-experimental3): right-click on your project, and select 'Manage NuGet Packages..'. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`.
7. Build and run the app.

### [Unpackaged console app](#tab/console)

To create an unpackaged app:

1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/)
2. Create an unpackaged C# console app project, by selecting the **Console App** template.
2. Ensure that your build configuration is set to `ARM64`.
3. Add `<WindowsPackageType>None</WindowsPackageType>` in your project file to declare it as unpackaged.
4. [Install Windows app runtime](/windows/apps/windows-app-sdk/downloads#experimental-release). 

To learn more, see [Tutorial: Build and deploy an unpackaged app using Preview and Experimental channels of the Windows App SDK](/windows/apps/windows-app-sdk/preview-experimental-unpackaged-tutorial?tabs=csharp-dotnet-preview3).

---

## 3. Check for model availability

When implementing an AI feature using Windows Copilot Runtime APIs, the app should first check for the availability of the AI model supporting that feature. Unlike typical Windows App SDK APIs where a developer can call on an API to immediately provide functionality or content, the Windows Copilot Runtime APIs rely on the model to be available on the app users machine.

To check if the model required by an AI feature is available on the user's device, begin by calling: `IsAvailable()`. This method will return `true` if the model being called is installed on the user's device. This method needs to be called before every call to the model.

If the model is not available on the user's device, the method `MakeAvailableAsync()` can be called to install the required model. The model installation will run in the background, and the user will be able to check on the install progress in the Windows Update page of the Settings application.

The `MakeAvailableAsync()` method has a status option which can show a loading UI. If the user has unsupported hardware, `MakeAvailableAsync()` will fail with an error.

Once the model is available, `CreateAsync()` can be called to create a new instance from a class that belongs to the model. The APIs that belong to that class can then be used in the app.

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

## Troubleshooting

If things are not working, first try running the [AI Dev Gallery app](#1-check-if-your-pc-is-correctly-configured) to see if it works correctly.

If this fails, ensure you have models installed on your machine. That can be verified by going to **System > AI Components** in the Settings app and see entries for different AI models. If not, then you may not be on right branch or something else.

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
