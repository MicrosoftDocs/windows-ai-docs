---
title: Get started building an app with Windows Copilot Runtime APIs
description: To use Windows Copilot Runtime APIs, you'll first need to confirm that your PC is set up correctly.
ms.topic: overview
ms.date: 04/23/2025
no-loc: [API, APIs]
dev_langs:
- csharp
- cpp
---

# Get started building an app with Windows Copilot Runtime APIs

This guide describes both hardware specifications and the steps required to configure a device and successfully build apps using the Windows Copilot Runtime APIs.

## Dependencies

Ensure that your PC supports Windows Copilot Runtime and that all dependencies are installed. You can choose to do this automatically (recommended) or manually.

#### [Automated dependency installation (recommended)](#tab/winget)

1. Download the [Windows Copilot Runtime configuration .winget file](https://github.com/hamza-usmani/winget-dsc/blob/wcr-docs-squad/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/configuration.winget) from the *winget-dsc* GitHub repo (click **...** > **Download** from the top of the page).

1. Next, double-click the file to open it with the Windows Package Manager client (or launch Terminal, navigate to the folder where you downloaded `configuration.winget`, and run the file using `winget configure configuration.winget`).

    This performs the following tasks:
    - Checks for minimum OS version.
    - Enables Developer Mode.
    - Installs Visual Studio Community Edition with WinUI and other required workloads.
    - Installs the Windows App SDK.

#### [Manual dependency installation](#tab/manual)

- Confirm that your device is a Copilot+ PC (we recommend the devices listed in the [Copilot+ PCs developer duide](/windows/ai/npu-devices/)).
- Install [Windows 11 Insider Preview build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/), or later (to check your OS version, run `winver` in Windows Search).
- Enable Developer Mode in **Settings** > **System** > **For developers** > **Developer Mode**.
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/) with the specific workloads and components for developing with WinUI and the Windows App SDK. For more details, see [Required workloads and components](/windows/apps/get-started/start-here#22-required-workloads-and-components).

---

## Build a new app

The following steps describe how to build your own app that uses Windows Copilot Runtime APIs (select the tab for your preferred UI framework).

#### [WinUI](#tab/winui)

1. In Visual Studio, create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.

:::image type="content" source="../images/winui-template.png" alt-text="A screenshot of the Visual Studio new Project UI with the WinUI template selected.":::

1. In **Solution Explorer**, right-click the project node, select **Properties** > **Application** > **General**, and ensure that the target framework is set to *.NET 8.0*, and the target OS is set to *10.0.22621* or later.

:::image type="content" source="../images/winui-project-properties-pane.png" alt-text="A screenshot of the Visual Studio project properties pane":::

1. Right-click the project node and select **Manage NuGet Packages..**.

1. In **NuGet Package Manager**, check the **Include prelease** checkbox, and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

:::image type="content" source="../images/winui-wasdk.png" alt-text="A screenshot of the Visual Studio nuget package manager with Microsoft.WindowsAppSDK 1.8.250410001-experimental1 selected.":::

1. Ensure that your build configuration is set to *ARM64*.

:::image type="content" source="../images/winui-arm64.png" alt-text="A screenshot of the Visual Studio build config set to ARM64":::

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first artificial intelligence (AI) API.

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

1. In **Solution Explorer**, right-click the **Dependencies** node and select **Manage Nuget Packages...**.

1. In **NuGet Package Manager**, check the **Include prelease** checkbox, and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first artificial intelligence (AI) API.

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

1. In **Solution Explorer**, right-click the **Dependencies** node > **Manage Nuget Packages...**.

1. In **NuGet Package Manager** > **Browse**, check **Include prelease**, and and select Windows App SDK version *1.8.250410001-experimental1*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first artificial intelligence (AI) API.

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
    > While clicking the project node and selecting the **Manage NuGet Packages..** option can be used to add the required package, the project file still needs to be edited to condition the package reference for just Windows builds if your app is also building for other platforms such as Android and iOS.

1. In **Solution Explorer**, right-click the project node, select **Properties**, and ensure that the Target Windows Framework is set to *10.0.22621* or later.

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first artificial intelligence (AI) API.

#### [Unpackaged console app](#tab/console)

1. In Visual Studio, create an unpackaged C# console app project by selecting the **Console App** template.

1. Ensure that your build configuration is set to *ARM64*.

1. Add `<WindowsPackageType>None</WindowsPackageType>` in your project file to declare it as unpackaged.

1. Install the Windows App SDK version *1.8.250410001-experimental1* runtime from [Latest Windows App SDK downloads](/windows/apps/windows-app-sdk/downloads#windows-app-sdk-18-experimental).

1. Build and run the app.

1. If the app launches succesfully, then continue to step 3 to add your first AI API.

For more info, see [Tutorial: Build and deploy an unpackaged app using Preview and Experimental channels of the Windows App SDK](/windows/apps/windows-app-sdk/preview-experimental-unpackaged-tutorial).

---

### 3. Add your first AI API

When implementing an AI feature using Windows Copilot Runtime APIs, your app should first check for the availability of the AI model that supports that feature.

The following snippet shows how to check for model availability and generate a response.

#### [WinUI](#tab/winui2)

1. In MainWindow.xaml, add a **TextBlock** to display the **LanguageModel** response.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of MainWindow.xaml.cs, add the following `using Microsoft.Windows.AI.Generative` directive.

    ```csharp
    using Microsoft.Windows.AI.Generative; 
    ```

1. In `MainWindow.xaml.cs`, replace the **MainWindow** class with the following code, which confirms the **LanguageModel** is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

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

            if (!LanguageModel.GetReadyState())
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

1. In MainWindow.xaml, add a **TextBlock** to display the **LanguageModel** response.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of MainWindow.xaml.cs, add the following `using Microsoft.Windows.AI.Generative` directive.

    ```csharp
    using Microsoft.Windows.AI.Generative; 
    ```

1. In MainWindow.xaml.cs, replace the **MainWindow** class with the following code, which confirms the **LanguageModel** is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

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

            if (!LanguageModel.GetReadyState())
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

1. In the Designer, drag a **Label** onto the page, and name it *OutputLabel*.

1. At the top of Form1.cs, add the following `using Microsoft.Windows.AI.Generative` directive.

    ```csharp
    using Microsoft.Windows.AI.Generative; 
    ```

1. In Form.cs, replace the **Form** class with the following code, which confirms the **LanguageModel** is available and then submits a prompt asking for the model to respond with the molecular formula of glucose.

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

            if (!LanguageModel.GetReadyState())
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

- In MainPage.xaml.cs, add a partial method definition as `partial void ChangeLanguageModelAvailability();` and call that partial method at the beginning of the **OnCounterClicked** method.
    ![Screenshot of OnCounterClicked.](../images/GetImage(4).png)
- In **Solution Explorer**, expand **Platforms**, right-click on **Windows**, select **Add** > **Class…**, type the name MainPage.cs, and click **Add**.
    ![Screenshot of Add New Item.](../images/GetImage(5).png)
- The new MainPage.cs should be shown in the editor window. Switch back to MainPage.xaml.cs to copy its namespace line.
    ![Screenshot of Namespace.](../images/GetImage(6).png)
- Switch back to the new MainPage.cs and replace its namespace line with the line from MainPage.xaml.cs. This is to make the **Platform\Windows** class a partial extension of the base **MainPage** class.
- To complete making MainPage.cs an extension, replace `internal` on the class declaration with `partial`.
    ![Screenshot of MainPage.cs.](../images/GetImage(7).png)
- Add a definition of the partial **ChangeLanguageModelAvailability** method defined earlier.

    ```csharp
        partial void ChangeLanguageModelAvailability() 
        { 
            try 
            { 
                bool readyState = Microsoft.Windows.AI.Generative.LanguageModel.GetReadyState(); 
                System.Diagnostics.Debug.WriteLine($"LanguageModel.GetReadyState: {readyState}"); 
            } 
            catch (Exception e) 
            { 
                System.Diagnostics.Debug.WriteLine($"LanguageModel is not available: {e}"); 
            } 
        }
    ```

1. Run the app again, click the "Click me" button, and observe the output in the Visual Studio debug output pane.

---

If you encounter any errors, it's typically because of your hardware or lack of model availability.

- The **GetReadyState** method checks whether the model required by an AI feature is available on the user's device. You must call this method before any call to the model.
- If the model isn't available on the user's device, then you can call the method **EnsureReadyAsync** to install the required model. Model installation runs in the background, and the user can check the install progress on the **Windows Settings** > **Windows Update** Settings page.
- The **EnsureReadyAsync** method has a status option that can show a loading UI. If the user has unsupported hardware, then **EnsureReadyAsync** will fail with an error.
- Once the model is available, you can call **CreateAsync** to create a new instance from a class that belongs to the model. You can then use the APIs that belong to that class.

### 4. Next steps: advanced tutorials and APIs

Now that you've successfully checked for model availability, explore the APIs further in the various Windows Copilot Runtime Tutorials.

- [Learn more about available Windows Copilot Runtime APIs](./index.md)
- [Phi Silica API Walkthrough](./phi-silica-tutorial.md)
- [Text Recognition API Walkthrough](./text-recognition-tutorial.md)
- [Imaging API Walkthrough](./imaging-tutorial.md)

### Troubleshooting

If you run into issues, see [Troubleshooting and FAQ](./troubleshooting.md).

### Related content

- [Developing responsible generative AI apps and features on Windows](../rai.md)
- [API reference for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
