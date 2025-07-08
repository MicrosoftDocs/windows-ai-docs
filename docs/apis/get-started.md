---
title: Get started building an app with Windows AI APIs
description: To use Windows AI APIs, you'll first need to confirm that your PC is set up correctly.
ms.topic: overview
ms.date: 05/16/2025
no-loc: [API, APIs]
dev_langs:
- csharp
- cpp
---

# Get started building an app with Windows AI APIs

Learn about the Windows AI API hardware requirements and how to configure your device to successfully build apps using the Windows AI APIs.

## Dependencies

Ensure that your PC supports Windows AI APIs and that all dependencies are installed. You can choose to do this automatically (recommended) or manually.

#### [Automated dependency installation (recommended)](#tab/winget)

1. Confirm that your device is a Copilot+ PC (we recommend the devices listed in the [Copilot+ PCs developer guide](../npu-devices/index.md)).

1. Run the following command in [Windows Terminal](/windows/terminal/).

   ```cmd
   winget configure https://raw.githubusercontent.com/microsoft/winget-dsc/refs/heads/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/learn_wcr.winget
   ```

   This runs a [WinGet Configuration file](https://github.com/microsoft/winget-dsc/blob/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/learn_wcr.winget) that performs the following tasks:

    - Checks for minimum OS version.
    - Enables Developer Mode.
    - Installs Visual Studio Community Edition with WinUI and other required workloads.
    - Installs the Windows App SDK.

#### [Manual dependency installation](#tab/manual)

- Confirm that your device is a Copilot+ PC (we recommend the devices listed in the [Copilot+ PCs developer guide](../npu-devices/index.md).
- Install [Windows 11 Insider Preview build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/), or later (to check your OS version, run `winver` from Windows Search).
- Enable Developer Mode in **Settings** > **System** > **For developers** > **Developer Mode**.
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/) with the specific workloads and components for developing with WinUI and the Windows App SDK. For more details, see [Required workloads and components](/windows/apps/get-started/start-here#22-required-workloads-and-components).

---

## Build a new app

The following steps describe how to build an app that uses Windows AI APIs (select the tab for your preferred UI framework).

#### [WinUI](#tab/winui)

1. In Visual Studio, create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.

   :::image type="content" source="../images/winui-template.png" alt-text="A screenshot of the Visual Studio new Project UI with the WinUI template selected.":::

1. In **Solution Explorer**, right-click the project node, select **Properties** > **Application** > **General**, and ensure that the target framework is set to *.NET 8.0*, and the target OS is set to *10.0.22621* or later.

   :::image type="content" source="../images/winui-project-properties-pane.png" alt-text="A screenshot of the Visual Studio project properties pane":::

1. Edit the Package.appxmanifest file (right click and select **View code**) and add the following snippets.

    - The `systemAIModels` capability to the `<Capabilities>` node:

       ```xml
       <Capabilities>
          <systemai:Capability Name="systemAIModels"/>
       </Capabilities>
       ```

    - The `systemai` namespace specifier to "IgnorableNamespaces" in the `<Package>` node:

        ```xml
        xmlns:systemai="http://schemas.microsoft.com/appx/manifest/systemai/windows10"
        IgnorableNamespaces="uap rescap systemai"
        ```
    - The max version tested in the `TargetDeviceFamily` element of the `<Dependencies>` node needs to be at least 10.0.26226.0:
      
       ```xml
       <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
       ```
       
1. Add the following to your .waproj, .csproj, or .vcxproj file. This step is necessary to ensure visual studio doesn't override the max version tested.

   ```xml
   <AppxOSMinVersionReplaceManifestVersion>false</AppxOSMinVersionReplaceManifestVersion>
   <AppxOSMaxVersionTestedReplaceManifestVersion>false</AppxOSMaxVersionTestedReplaceManifestVersion>
   ```

1. Right-click the project node and select **Manage NuGet Packages...**.

1. In **NuGet Package Manager**, check the **Include prerelease** checkbox, and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

   :::image type="content" source="../images/winui-wasdk.png" alt-text="A screenshot of the Visual Studio nuget package manager with Microsoft.WindowsAppSDK 1.8.250410001-experimental1 selected.":::

1. Ensure that your build configuration is set to *ARM64*.

   :::image type="content" source="../images/winui-arm64.png" alt-text="A screenshot of the Visual Studio build config set to ARM64.":::

1. Build and run your app.

1. If the app launches succesfully, then continue to [Add your first AI API](#add-your-first-ai-api). Otherwise, see [Troubleshooting](#troubleshooting).

#### [WPF](#tab/wpf)

1. In Visual Studio, create a new WPF project by selecting the **WPF Application** template.

1. In **Solution Explorer**, right-click the project node and select  **Edit Project File** to open as XML. Replace everything inside `<PropertyGroup>` with the following:

    ```xml
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    <Nullable>enable</Nullable>
    <UseWPF>true</UseWPF>
    <ImplicitUsings>enable</ImplicitUsings>
    <WindowsPackageType>None</WindowsPackageType>
    ```

1. Edit the Package.appxmanifest file (right click and select **View code**) and add the following snippets.

    - The `systemAIModels` capability to the `<Capabilities>` node:

       ```xml
       <Capabilities>
          <systemai:Capability Name="systemAIModels"/>
       </Capabilities>
       ```

    - The `systemai` namespace specifier to "IgnorableNamespaces" in the `<Package>` node:

        ```xml
        xmlns:systemai="http://schemas.microsoft.com/appx/manifest/systemai/windows10"
        IgnorableNamespaces="uap rescap systemai"
        ```
    - The max version tested in the `TargetDeviceFamily` element of the `<Dependencies>` node needs to be at least 10.0.26226.0:
      
       ```xml
       <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
       ```

1. Add the following to your .waproj, .csproj, or .vcxproj file. This step is necessary to ensure visual studio doesn't override the max version tested.

   ```xml
   <AppxOSMinVersionReplaceManifestVersion>false</AppxOSMinVersionReplaceManifestVersion>
   <AppxOSMaxVersionTestedReplaceManifestVersion>false</AppxOSMaxVersionTestedReplaceManifestVersion>
   ```

1. In **Solution Explorer**, right-click the **Dependencies** node and select **Manage Nuget Packages...**.

1. In **NuGet Package Manager**, check the **Include prerelease** checkbox, and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to [Add your first AI API](#add-your-first-ai-api). Otherwise, see [Troubleshooting](#troubleshooting).

For more info, see [Configure your WPF project for Windows App SDK support](/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/wpf-plus-winappsdk#configure-your-wpf-project-for-windows-app-sdk-support).

#### [WinForms](#tab/winforms)

1. In Visual Studio, create a new WinForms project by selecting the **Windows Forms App** template.

1. In **Solution Explorer**, right-click the project node > **Edit Project File** to open as XML. Replace everything inside `<PropertyGroup>` with the following:

    ```xml
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
    <WindowsPackageType>None</WindowsPackageType>
    ```

1. Edit the Package.appxmanifest file (right click and select **View code**) and add the following snippets.

    - The `systemAIModels` capability to the `<Capabilities>` node:

       ```xml
       <Capabilities>
          <systemai:Capability Name="systemAIModels"/>
       </Capabilities>
       ```

    - The `systemai` namespace specifier to "IgnorableNamespaces" in the `<Package>` node:

        ```xml
        xmlns:systemai="http://schemas.microsoft.com/appx/manifest/systemai/windows10"
        IgnorableNamespaces="uap rescap systemai"
        ```
    - The max version tested in the `TargetDeviceFamily` element of the `<Dependencies>` node needs to be at least 10.0.26226.0:
      
       ```xml
       <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
       ```
       
1. Add the following to your .waproj, .csproj, or .vcxproj file. This step is necessary to ensure visual studio doesn't override the max version tested.

   ```xml
   <AppxOSMinVersionReplaceManifestVersion>false</AppxOSMinVersionReplaceManifestVersion>
   <AppxOSMaxVersionTestedReplaceManifestVersion>false</AppxOSMaxVersionTestedReplaceManifestVersion>
   ```

1. In **Solution Explorer**, right-click the **Dependencies** node > **Manage Nuget Packages...**.

1. In **NuGet Package Manager** > **Browse**, check **Include prerelease**, and and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to [Add your first AI API](#add-your-first-ai-api). Otherwise, see [Troubleshooting](#troubleshooting).

For more info, see [Configure your WinForms project for Windows App SDK support](/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/wpf-plus-winappsdk#configure-your-wpf-project-for-windows-app-sdk-support).

#### [.NET MAUI](#tab/maui)

1. Create a MAUI project by following the instructions at [Build your first .NET MAUI app](/dotnet/maui/get-started/first-app?view=net-maui-9.0&tabs=vswin&pivots=devices-windows&preserve-view=true).

1. In **Solution Explorer**, right-click the project node > **Edit Project File** to open as XML.

1. At the bottom of the project file, add these lines to reference the correct Microsoft.WindowsAppSDK package version (when compiling for the Windows platform):

    ```xml
    <ItemGroup Condition="$([MSBuild]::GetTargetPlatformIdentifier('$(TargetFramework)')) == 'windows'">
       <PackageReference Include="Microsoft.WindowsAppSDK" Version="1.8.250410001-experimental1"/> 
    </ItemGroup>
    ```

    >[!NOTE]
    > While clicking the project node and selecting the **Manage NuGet Packages...** option can be used to add the required package, if your app is also building for other platforms, such as Android and iOS, the project file still needs to be edited to condition the package reference for Windows-only builds.

1. Add the following to your .waproj, .csproj, or .vcxproj file. This step is necessary to ensure visual studio doesn't override the max version tested.

   ```xml
   <AppxOSMinVersionReplaceManifestVersion>false</AppxOSMinVersionReplaceManifestVersion>
   <AppxOSMaxVersionTestedReplaceManifestVersion>false</AppxOSMaxVersionTestedReplaceManifestVersion>
   ```

1. In **Solution Explorer**, right-click the project node, select **Properties**, and ensure that the Target Windows Framework is set to *10.0.22621* or later.

1. Edit the Package.appxmanifest file (right click and select **View code**) and add the following snippets.

    - The `systemAIModels` capability to the `<Capabilities>` node:

       ```xml
       <Capabilities>
          <systemai:Capability Name="systemAIModels"/>
       </Capabilities>
       ```

    - The `systemai` namespace specifier to "IgnorableNamespaces" in the `<Package>` node:

        ```xml
        xmlns:systemai="http://schemas.microsoft.com/appx/manifest/systemai/windows10"
        IgnorableNamespaces="uap rescap systemai"
        ```
    - The max version tested in the `TargetDeviceFamily` element of the `<Dependencies>` node needs to be at least 10.0.26226.0:
      
       ```xml
       <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
       ```

1. Build and run your app.

1. If the app launches succesfully, then continue to [Add your first AI API](#add-your-first-ai-api). Otherwise, see [Troubleshooting](#troubleshooting).

---

## Add your first AI API

When implementing a feature using Windows AI APIs, your app should first check for the availability of the AI model that supports that feature.

The following snippet shows how to check for model availability and generate a response.

#### [WinUI](#tab/winui2)

1. In MainWindow.xaml, add a **TextBlock** to display the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) response.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of MainWindow.xaml.cs, add the following `using Microsoft.Windows.AI` directive.

    ```csharp
    using Microsoft.Windows.AI; 
    ```

1. In `MainWindow.xaml.cs`, replace the **MainWindow** class with the following code, which confirms the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

    ```csharp
    public sealed partial class MainWindow : Window
    {
        public MainWindow()
        {
            this.InitializeComponent();
            InitAI();
        }
    
        private async void InitAI()
        {
            OutputText.Text = "Loading..";

            if (LanguageModel.GetReadyState() == AIFeatureReadyState.EnsureNeeded)
            {
                var result = await LanguageModel.EnsureReadyAsync();
                if (result.Status != PackageDeploymentStatus.CompletedSuccess)
                {
                    throw new Exception(result.ExtendedError().Message);
                }
            }            

            using LanguageModel languageModel = 
               await LanguageModel.CreateAsync();

            string prompt = "Provide the molecular formula of glucose.";
            var result = await languageModel.GenerateResponseAsync(prompt);
            OutputText.Text = result.Response;
        }
    }
    ```

1. Build and run the app.

1. The formula for glucose should appear in the text block.

#### [WPF](#tab/wpf2)

1. In MainWindow.xaml, add a **TextBlock** to display the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) response.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of MainWindow.xaml.cs, add the following `using Microsoft.Windows.AI` directive.

    ```csharp
    using Microsoft.Windows.AI; 
    ```

1. In MainWindow.xaml.cs, replace the **MainWindow** class with the following code, which confirms the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

    ```csharp
    public sealed partial class MainWindow : Window
    {
        public MainWindow()
        {
            this.InitializeComponent();
            InitAI();
        }
    
        private async void InitAI()
        {
            OutputText.Text = "Loading..";

            if (LanguageModel.GetReadyState() == AIFeatureReadyState.EnsureNeeded)
            {
                var result = await LanguageModel.EnsureReadyAsync();
                if (result.Status != PackageDeploymentStatus.CompletedSuccess)
                {
                    throw new Exception(result.ExtendedError().Message);
                }
            }            

            using LanguageModel languageModel = await LanguageModel.CreateAsync();

            string prompt = "Provide the molecular formula for glucose.";
            var result = await languageModel.GenerateResponseAsync(prompt);
            OutputText.Text = result.Response;
        }
    }
    ```

1. Build and run the app.

1. The formula for glucose should appear in the text block.

#### [WinForms](#tab/winforms2)

1. In the [Windows Forms Designer](/visualstudio/designers/windows-forms-designer-overview), drag a **Label** onto the page, and name it *OutputLabel*.

1. At the top of Form1.cs, add the following `using Microsoft.Windows.AI` directive.

    ```csharp
    using Microsoft.Windows.AI; 
    ```

1. In Form.cs, replace the **Form** class with the following code, which confirms the [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

    ```csharp
    public partial class Form1 : Window
    {
        public Form1()
        {
            this.InitializeComponent();
            InitAI();
        }

        private async void InitAI()
        {
            OutputLabel.Text = "Loading..";

            if (LanguageModel.GetReadyState() == AIFeatureReadyState.EnsureNeeded)
            {
                var result = await LanguageModel.EnsureReadyAsync();
                if (result.Status != PackageDeploymentStatus.CompletedSuccess)
                {
                    throw new Exception(result.ExtendedError().Message);
                }
            }            

            using LanguageModel languageModel = await LanguageModel.CreateAsync();

            string prompt = "Provide the molecular formula for glucose.";
            var result = await languageModel.GenerateResponseAsync(prompt);
            OutputLabel.Text = result.Response;
        }
    }
    ```

1. Build and run the app.

1. The formula for glucose should appear in the text block.

#### [.NET MAUI](#tab/maui2)

See [Invoke platform code](/dotnet/maui/platform-integration/invoke-platform-code?view=net-maui-9.0&preserve-view=true) for details on adding platform-specific code to a MAUI app.

For this example, we use the partial classes and partial methods approach to put the Windows code in the Platform\Windows folder.

1. In MainPage.xaml.cs, add a partial method definition as `partial void ChangeLanguageModelAvailability();` and call that partial method from the **OnCounterClicked** handler for the `CounterBtn` button created by the .NET MAUI App template.
1. In **Solution Explorer**, expand **Platforms**, right-click on **Windows**, select **Add** > **Class…**, type the name MainPage.cs, and click **Add**.
1. The new MainPage.cs should be shown in the editor window. Switch back to MainPage.xaml.cs to copy its namespace line.
1. Switch back to the new MainPage.cs and replace its namespace line with the line from MainPage.xaml.cs. This is to make the **Platform\Windows** class a partial extension of the base **MainPage** class.
1. To complete making MainPage.cs an extension, replace `internal` on the class declaration with `partial`.
1. Add the following code to the **ChangeLanguageModelAvailability** partial method defined in step 1.

   ```csharp
   partial void ChangeLanguageModelAvailability() 
   { 
      try 
      { 
         AIFeatureReadyState readyState = Microsoft.Windows.AI.LanguageModel.GetReadyState(); 
         System.Diagnostics.Debug.WriteLine($"LanguageModel.GetReadyState: {readyState}"); 
      } 
      catch (Exception e) 
      { 
         System.Diagnostics.Debug.WriteLine($"LanguageModel is not available: {e}"); 
      } 
   }
   ```

1. Run the app again, click the **Click me** button, and observe the output in the Visual Studio debug output pane.

---

### Advanced tutorials and APIs

Now that you've successfully checked for model availability, explore the APIs further in the various Windows AI API tutorials.

- [Learn more about available Windows AI APIs](./index.md)
- [Phi Silica API Walkthrough](./phi-silica-tutorial.md)
- [Text Recognition API Walkthrough](./text-recognition-tutorial.md)
- [Imaging API Walkthrough](./imaging-tutorial.md)

### Troubleshooting

If you encounter any errors, it's typically because of your hardware or the absence of a required model.

- The **GetReadyState** method checks whether the model required by an AI feature is available on the user's device. You must call this method before any call to the model.
- If the model isn't available on the user's device, then you can call the method **EnsureReadyAsync** to install the required model. Model installation runs in the background, and the user can check the install progress on the **Windows Settings** > **Windows Update** Settings page.
- The **EnsureReadyAsync** method has a status option that can show a loading UI. If the user has unsupported hardware, then **EnsureReadyAsync** will fail with an error.

See [Windows AI API troubleshooting and FAQ](./troubleshooting.md) for more assistance.

## See also

- [Developing responsible generative AI apps and features on Windows](../rai.md)
- [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
