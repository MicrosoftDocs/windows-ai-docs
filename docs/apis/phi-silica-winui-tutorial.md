---
title: "Tutorial: Build a chat app with Phi Silica and WinUI 3"
description: Step-by-step guide to building a WinUI 3 app that uses the Phi Silica on-device language model for text generation, summarization, and rewriting.
ms.topic: tutorial
ms.date: 03/14/2026
dev_langs:
- csharp
---

# Tutorial: Build a chat app with Phi Silica and WinUI 3

This tutorial builds a simple WinUI 3 desktop app that uses the [Phi Silica](phi-silica.md) on-device language model. The finished app lets you type a prompt, choose a skill (chat, summarize, or rewrite), and see the response generated locally on your device — no cloud call, no API key.

> [!IMPORTANT]
> Phi Silica requires a **Copilot+ PC**. The model runs on the device's NPU and is not available on standard PCs. If you don't have a Copilot+ PC, see [Foundry Local](../foundry-local/get-started.md) as an alternative that runs on any modern Windows PC.

> [!IMPORTANT]
> The Phi Silica APIs are part of a Limited Access Feature. For more information or to request an unlock token, use the [LAF Access Token Request Form](https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409).

> [!NOTE]
> Phi Silica features are not available in China.

## What you'll build

A WinUI 3 app with:
- A prompt input box
- A skill selector (Chat / Summarize / Rewrite)
- A response display area
- Status feedback while the model loads

:::image type="content" source="../images/phi-silica-winui-app.png" alt-text="Screenshot of the finished Phi Silica chat app showing a prompt and response.":::

## Prerequisites

1. **Copilot+ PC** — required for NPU acceleration. See the [Copilot+ PCs developer guide](../npu-devices/index.md).

2. **Windows 11 build 26100 or later** (25H2) — check with `winver` from Windows Search.

3. **Developer Mode** enabled — Windows Settings → System → For developers → Developer Mode.

4. **Visual Studio 2022** with the **Windows application development** workload installed. See [Required workloads and components](/windows/apps/get-started/start-here#22-required-workloads-and-components).

5. **Windows App SDK 2.0 preview NuGet package** — `Microsoft.WindowsAppSDK` version `2.0.0-preview1`. You'll install this in the steps below.

6. **Phi Silica LAF unlock token** — request one using the link above. Without it, calls to the API will fail with an access denied error.

> **Tip:** Run the automated setup command in Windows Terminal to install Visual Studio and the Windows App SDK in one step:
> ```cmd
> winget configure https://raw.githubusercontent.com/microsoft/winget-dsc/refs/heads/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/learn_wcr.winget
> ```

## Step 1: Create the project

1. Open Visual Studio.

1. Select **Create a new project**, search for **WinUI**, and select **Blank App, Packaged (WinUI 3 in Desktop)**.

1. Name the project `PhiSilicaChat`, choose a location, and select **Create**.

1. In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.

1. Check **Include prerelease**, search for `Microsoft.WindowsAppSDK`, select version `2.0.0-preview1`, and click **Install**.

1. Set the build configuration to **ARM64** in the toolbar dropdown.

1. In the toolbar's launch profile dropdown (next to the play button), select **PhiSilicaChat (Package)** — not the unpackaged profile.

    > [!IMPORTANT]
    > The `LanguageModel` API only works in a **packaged MSIX app**. If you accidentally run the unpackaged profile, `GetReadyState()` will fail with a COM error. Always use the **MsixPackage** launch profile.

## Step 2: Configure the app manifest

The app needs the `systemAIModels` capability and a minimum OS version of 10.0.26100.0 to access Phi Silica.

1. In **Solution Explorer**, right-click `Package.appxmanifest` and select **View Code**.

2. Find the opening `<Package` tag and **replace the entire `<Package ...>` opening tag** with this version:

    ```xml
    <Package
      xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
      xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
      xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
      xmlns:systemai="http://schemas.microsoft.com/appx/manifest/systemai/windows10"
      IgnorableNamespaces="uap rescap systemai">
    ```

    > [!IMPORTANT]
    > The `xmlns:systemai` declaration **must appear in the `<Package>` opening tag**. Adding `<systemai:Capability>` without the namespace declaration here causes an "undeclared prefix" parse error.

3. Find the `<Dependencies>` element and **replace it entirely** — raising `MinVersion` to `10.0.26100.0` is required. If the minimum version is lower, Windows silently ignores the `systemai:Capability` and `GetReadyState()` throws "Not declared by app":

    ```xml
    <Dependencies>
      <TargetDeviceFamily Name="Windows.Universal" MinVersion="10.0.26100.0" MaxVersionTested="10.0.26300.0" />
      <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.26100.0" MaxVersionTested="10.0.26300.0" />
    </Dependencies>
    ```

4. Find the `<Capabilities>` element and **replace it entirely** with:

    ```xml
    <Capabilities>
      <rescap:Capability Name="runFullTrust"/>
      <systemai:Capability Name="systemAIModels"/>
    </Capabilities>
    ```

## Step 3: Configure the project file

Prevent Visual Studio from overriding the manifest version settings.

1. In **Solution Explorer**, right-click the **project name** (the top-level node with the C# icon, just below the solution) and select **Edit Project File**. This opens the `.csproj` file directly in the editor — it isn't shown as a file in Solution Explorer.

2. Inside the `<PropertyGroup>` element, add:

    ```xml
    <AppxOSMinVersionReplaceManifestVersion>false</AppxOSMinVersionReplaceManifestVersion>
    <AppxOSMaxVersionTestedReplaceManifestVersion>false</AppxOSMaxVersionTestedReplaceManifestVersion>
    ```

## Step 4: Build the UI

Replace the contents of `MainWindow.xaml` with the following:

```xml
<Window
    x:Class="PhiSilicaChat.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Phi Silica Chat">

    <Grid Margin="24" RowSpacing="12">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Phi Silica Chat" Style="{StaticResource TitleTextBlockStyle}"/>

        <TextBox Grid.Row="1"
                 x:Name="PromptBox"
                 PlaceholderText="Enter your prompt here..."
                 AcceptsReturn="True"
                 MinHeight="80"
                 TextWrapping="Wrap"/>

        <StackPanel Grid.Row="2" Orientation="Horizontal" Spacing="12">
            <ComboBox x:Name="SkillSelector" SelectedIndex="0" MinWidth="160">
                <ComboBoxItem Content="Chat (no skill)"/>
                <ComboBoxItem Content="Summarize"/>
                <ComboBoxItem Content="Rewrite"/>
            </ComboBox>
            <Button x:Name="SendButton" Content="Generate" Click="OnSendClicked" Style="{StaticResource AccentButtonStyle}"/>
        </StackPanel>

        <ScrollViewer Grid.Row="3" VerticalScrollBarVisibility="Auto" Margin="0,8,0,0">
            <TextBlock x:Name="ResponseText"
                       TextWrapping="Wrap"
                       IsTextSelectionEnabled="True"
                       FontSize="14"/>
        </ScrollViewer>

        <TextBlock Grid.Row="4" x:Name="StatusText" Opacity="0.6" FontSize="12"/>
    </Grid>
</Window>
```

## Step 5: Add the code-behind

> [!NOTE]
> The `LanguageModelSkill` enum (`Summarize`, `Rewrite`) is not available in all experimental builds of the Windows App SDK. This tutorial uses **prompt engineering** to achieve the same result — instructing the model via the prompt text. When the Skill API becomes available in a stable release, you can replace the prompt string construction with `new LanguageModelOptions { Skill = LanguageModelSkill.Summarize }`.

Replace the contents of `MainWindow.xaml.cs` with the following:

```csharp
using Microsoft.UI.Xaml;
using Microsoft.UI.Xaml.Controls;
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Text;
using System;
using Windows.ApplicationModel;

namespace PhiSilicaChat;

public sealed partial class MainWindow : Window
{
    private LanguageModel? _languageModel;

    public MainWindow()
    {
        InitializeComponent();
        InitializeModelAsync();
    }

    private async void InitializeModelAsync()
    {
        SendButton.IsEnabled = false;
        StatusText.Text = "Checking model availability...";

        // Unlock the Limited Access Feature.
        // Replace these values with the token and attestation string provided by Microsoft.
        // Request your token at https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409
        var access = LimitedAccessFeatures.TryUnlockFeature(
            "com.microsoft.windows.ai.languagemodel",
            "YOUR_TOKEN_HERE",
            "YOUR_ATTESTATION_STRING_HERE");

        if (access.Status != LimitedAccessFeatureStatus.Available &&
            access.Status != LimitedAccessFeatureStatus.AvailableWithoutToken)
        {
            StatusText.Text = $"Feature access denied (LAF status: {access.Status}). Check your token.";
            return;
        }

        try
        {
            var readyState = LanguageModel.GetReadyState();

            if (readyState == AIFeatureReadyState.NotReady)
            {
                StatusText.Text = "Model not ready — installing. This may take a few minutes...";
                var ensureResult = await LanguageModel.EnsureReadyAsync();

                if (ensureResult.ExtendedError != null)
                {
                    StatusText.Text = $"Model installation failed: {ensureResult.ExtendedError.Message}";
                    return;
                }
            }
            else if (readyState == AIFeatureReadyState.NotSupportedOnCurrentSystem)
            {
                StatusText.Text = "Phi Silica is not supported on this device. A Copilot+ PC is required.";
                ResponseText.Text = "Phi Silica requires a Copilot+ PC with an NPU.\n\n" +
                                     "For on-device AI on any Windows PC, see Foundry Local:\n" +
                                     "https://learn.microsoft.com/windows/ai/foundry-local/get-started";
                return;
            }

            _languageModel = await LanguageModel.CreateAsync();
            StatusText.Text = "Model ready.";
            SendButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            StatusText.Text = $"Model init failed: {ex.Message}";
        }
    }

        if (readyState == AIFeatureReadyState.NotReady)
        {
            StatusText.Text = "Model not ready — installing. This may take a few minutes...";
            var ensureResult = await LanguageModel.EnsureReadyAsync();

            if (ensureResult.ExtendedError != null)
            {
                StatusText.Text = $"Model installation failed: {ensureResult.ExtendedError.Message}";
                return;
            }
        }
        else if (readyState == AIFeatureReadyState.NotSupportedOnCurrentSystem)
        {
            // This device does not have a compatible NPU or is not a Copilot+ PC.
            // Consider falling back to Foundry Local or an Azure OpenAI endpoint.
            StatusText.Text = "Phi Silica is not supported on this device. A Copilot+ PC is required.";
            ResponseText.Text = "Phi Silica requires a Copilot+ PC with an NPU.\n\n" +
                                 "For on-device AI on any Windows PC, see Foundry Local:\n" +
                                 "https://learn.microsoft.com/windows/ai/foundry-local/get-started";
            return;
        }

        _languageModel = await LanguageModel.CreateAsync();
        StatusText.Text = "Model ready.";
        SendButton.IsEnabled = true;
    }

    private async void OnSendClicked(object sender, RoutedEventArgs e)
    {
        if (_languageModel is null) return;

        string prompt = PromptBox.Text.Trim();
        if (string.IsNullOrEmpty(prompt)) return;

        SendButton.IsEnabled = false;
        ResponseText.Text = string.Empty;
        StatusText.Text = "Generating response...";

        try
        {
            string fullPrompt;
            int skillIndex = SkillSelector.SelectedIndex;

            if (skillIndex == 1)
            {
                // Summarize: inject an instruction into the prompt
                fullPrompt = $"Summarize the following text concisely:\n\n{prompt}";
            }
            else if (skillIndex == 2)
            {
                // Rewrite: inject an instruction into the prompt
                fullPrompt = $"Rewrite the following text to be clearer and more professional:\n\n{prompt}";
            }
            else
            {
                // Plain chat
                fullPrompt = prompt;
            }

            var result = await _languageModel.GenerateResponseAsync(fullPrompt);
            ResponseText.Text = result.Text;
            StatusText.Text = "Done.";
        }
        catch (Exception ex)
        {
            ResponseText.Text = $"Error: {ex.Message}";
            StatusText.Text = "An error occurred.";
        }
        finally
        {
            SendButton.IsEnabled = true;
        }
    }
}
```

## Step 6: Add your LAF token

Phi Silica is a Limited Access Feature. Before building, replace the placeholder values in `InitializeModelAsync` with your actual token and attestation string.

1. Submit the [LAF Access Token Request Form](https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409).

2. When you receive your token email, find your **Package Family Name** by deploying the app once (**Build → Deploy Solution**) then running in PowerShell:

    ```powershell
    Get-AppxPackage | Where-Object {$_.Name -like "*PhiSilicaChat*"} | Select-Object PackageFamilyName
    ```

    > [!TIP]
    > If your project `Identity Name` is a GUID (the default for new projects), search by that value instead: `Get-AppxPackage | Where-Object {$_.Name -like "*YOUR-GUID*"}`

3. Reply to the token email with your Package Family Name. Microsoft will send you a token value and attestation string.

4. In `MainWindow.xaml.cs`, replace the placeholder values:

    ```csharp
    LimitedAccessFeatures.TryUnlockFeature(
        "com.microsoft.windows.ai.languagemodel",
        "YOUR_TOKEN_HERE",           // ← replace with token from email
        "YOUR_ATTESTATION_HERE");    // ← replace with full attestation string from email
    ```

    > [!IMPORTANT]
    > The token is bound to your app's Package Family Name. Do not commit it to a public repository.

## Step 7: Build and run

1. Confirm the build configuration is **ARM64**.

1. Press **F5** to build and run.

1. Wait for the status bar to show **"Model ready."**

1. Type a prompt, select a skill, and click **Generate**.

**Try these prompts to test each skill:**

| Skill | Example prompt |
|---|---|
| Chat | `What are the differences between WinUI 3 and WPF?` |
| Summarize | Paste a long article or documentation section |
| Rewrite | `make this formal: hey can u help me fix this bug` |

## Troubleshooting

**Status shows "not supported on this device"**  
Your PC either isn't a Copilot+ PC or doesn't meet the minimum Windows version (build 26200+). Check `winver` and confirm your device has an NPU.

**Build error: namespace not found**  
Confirm `Microsoft.WindowsAppSDK` `1.8.250410001-experimental1` (or later) is installed and the build is set to **ARM64** (not x64 or AnyCPU).

**API returns access denied / E_ACCESSDENIED**  
The Phi Silica API requires a Limited Access Feature unlock token. Request one at the [LAF Access Token Request Form](https://go.microsoft.com/fwlink/?linkid=2271232&c1cid=04x409). The token must be registered before calls will succeed.

**EnsureReadyAsync fails or hangs**  
Check **Settings > Windows Update** for the AI model download progress. The model download requires an internet connection and may take several minutes.

## Next steps

- [Phi Silica overview and full API reference](phi-silica.md)
- [LoRA fine-tuning for Phi Silica](phi-silica-lora.md)
- [Foundry Local — on-device AI for any Windows PC](../foundry-local/get-started.md)
- [Content moderation with Windows AI APIs](content-moderation.md)
- [AI Dev Gallery — browse more samples](https://github.com/microsoft/ai-dev-gallery/)
