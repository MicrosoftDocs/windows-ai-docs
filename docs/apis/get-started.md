---
title: Get started building an app with Windows Copilot Runtime APIs
description: To use Windows Copilot Runtime APIs, you'll first need to confirm that your PC is set up correctly.
ms.topic: overview
ms.date: 04/16/2025
no-loc: [API, APIs]
dev_langs:
- csharp
- cpp
---

# Get started building an app with Windows Copilot Runtime APIs

To use Windows Copilot Runtime APIs, you'll first need to confirm that your PC is set up correctly. Follow these steps:

### 1. Prerequisites

First, let's make sure that your PC supports Windows Copilot Runtime APIs; and install any dependencies as you go. There are two ways to do that, both of them detailed in the two tabs below:

1. In an automated way via the WinGet (Windows Package Manager) configuration file.
1. Or by checking and installing dependencies manually.

#### [Automated dependency installation (recommended)](#tab/winget)

The automated way is to obtain and run a Windows Copilot Runtime configuration `.winget` file.

1. Obtain the [Windows Copilot Runtime configuration .winget file](https://github.com/hamza-usmani/winget-dsc/blob/wcr-docs-squad/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/configuration.winget) in the *winget-dsc* GitHub repo by first clicking that link. Then, to download the `configuration.winget` configuration file, click **...** (in the top-right) > **Download**.

    This file performs the following:
    * Checks for minimum OS version.
    * Enables developer mode.
    * Installs Visual Studio Community Edition with the WinUI and required workloads.
    * Installs the Windows App SDK.

1. Next, there are two options. One is to double-click the file to open it with the Windows Package Manager client. Another is to launch Terminal, navigate to the folder to which you downloaded `configuration.winget`, and run the file via the following command:

    ```console
    winget configure configuration.winget
    ```

#### [Manual dependency installation](#tab/manual)

* Your PC must be a Copilot+ PC, and be on the list in [Copilot+ PCs developer guide](/windows/ai/npu-devices/).
* The version of Windows installed on your device must be [Windows 11 Insider Preview build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/), or later. To check your OS version, run `winver` in Windows Search.
* Enable Developer Mode in **Settings** > **System** > **For developers** > **Developer Mode**.
* Install [Visual Studio](https://visualstudio.microsoft.com/downloads/).
* While installing Visual Studio, you need to install certain workloads and components required for developing with WinUI and the Windows App SDK. Follow the steps in [Required workloads and components](/windows/apps/get-started/start-here#22-required-workloads-and-components).

---

### 2. Start building a new app

To build your own app that uses Windows Copilot Runtime APIs, follow the instructions for your preferred UI framework below:

#### [WinUI](#tab/winui)

1. In Visual Studio, create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.

:::image type="content" source="../images/winui-template.png" alt-text="A screenshot of the Visual Studio new Project UI with the WinUI template selected.":::

1. In **Solution Explorer**, right-click the project node > **Properties** > **Application** > **General**, and ensure that the target framework is set to *.NET 8.0*, and the target OS is set to *10.0.22621* or later.

:::image type="content" source="../images/winui-project-properties-pane.png" alt-text="A screenshot of the Visual Studio project properties pane":::

1. Right-click the project node, and select **Manage NuGet Packages..**. Check the **Include prelease** checkbox, and select Windows App SDK version *1.7.250127003-experimental3*. Click **Install** or **Update**.

:::image type="content" source="../images/winui-wasdk.png" alt-text="A screenshot of the Visual Studio nuget package with WASDK experimental 1.7.":::

1. Ensure that your build configuration is set to *ARM64*.

:::image type="content" source="../images/winui-arm64.png" alt-text="A screenshot of the Visual Studio build config set to ARM64":::

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first AI API.

#### [WPF](#tab/wpf)

1. In Visual Studio, create a new WPF project by selecting the **WPF Application** template.

1. In **Solution Explorer**, right-click the project node > **Edit Project File** to open as XML. Replace everything inside `<PropertyGroup>` with the following:

    ```xml
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    <Nullable>enable</Nullable>
    <UseWPF>true</UseWPF>
    <ImplicitUsings>enable</ImplicitUsings>
    <WindowsPackageType>None</WindowsPackageType>
    ```

1. In **Solution Explorer**, right-click the **Dependencies** node > **Manage Nuget Packages...**.

1. In **NuGet Package Manager** > **Browse**, check **Include prelease**, and and select Windows App SDK version *1.7.250127003-experimental3*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first AI API.

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

1. In **NuGet Package Manager** > **Browse**, check **Include prelease**, and and select Windows App SDK version *1.7.250127003-experimental3*. Click **Install** or **Update**.

1. Build and run your app.

1. If the app launches succesfully, then continue to step 3 to add your first AI API.

For more info, see [Configure your WinForms project for Windows App SDK support](/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/wpf-plus-winappsdk#configure-your-wpf-project-for-windows-app-sdk-support).

#### [Unpackaged console app](#tab/console)

1. In Visual Studio, create an unpackaged C# console app project by selecting the **Console App** template.

1. Ensure that your build configuration is set to *ARM64*.

1. Add `<WindowsPackageType>None</WindowsPackageType>` in your project file to declare it as unpackaged.

1. Install the Windows App SDK runtime from [Experimental release](/windows/apps/windows-app-sdk/downloads#experimental-release).

1. Build and run the app.

1. If the app launches succesfully, then continue to step 3 to add your first AI API.

For more info, see [Tutorial: Build and deploy an unpackaged app using Preview and Experimental channels of the Windows App SDK](/windows/apps/windows-app-sdk/preview-experimental-unpackaged-tutorial).

---

### 3. Add your first AI API

When you're implementing an AI feature by using Windows Copilot Runtime APIs, your app should first check for the availability of the AI model that supports that feature. So add the following code to check for model availability, and to generate a response.

#### [WinUI](#tab/winui2)

1. In `MainWindow.xaml`, add the following XAML.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of `MainWindow.xaml.cs`, add the following `using` directive.

    ```csharp
    using Microsoft.Windows.AI.Generative; 
    ```

1. In `MainWindow.xaml.cs`, replace the **MainWindow** class with the following.

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
            if (!LanguageModel.IsAvailable())
            {
                await LanguageModel.MakeAvailableAsync();
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

#### [WPF](#tab/wpf2)

1. In `MainWindow.xaml`, add the following XAML.

    ```xml
    <TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
    ```

1. At the top of `MainWindow.xaml.cs`, add the following `using` directive.

    ```csharp
    using Microsoft.Windows.AI.Generative; 
    ```

1. In `MainWindow.xaml.cs`, replace the **MainWindow** class with the following.

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
            if (!LanguageModel.IsAvailable())
            {
                await LanguageModel.MakeAvailableAsync();
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

1. In the Designer, drag a **Label** on to the page, and rename it to *OutputLabel*.

1. At the top of `Form1.cs`, add the following `using` directive.

```csharp
using Microsoft.Windows.AI.Generative; 
```

1. In `Form.cs`, replace the **Form** class with the following.

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
            if (!LanguageModel.IsAvailable())
            {
                await LanguageModel.MakeAvailableAsync();
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

---

If you encounter any errors, then it might be because of your hardware, or lack of model availability.

* The method **IsAvailable** checks whether the model required by an AI feature is available on the user's device (it returns `true` if the model being called is installed on the user's device). You need to call that method before every call to the model.
* If the model isn't available on the user's device, then you can call the method **MakeAvailableAsync** to install the required model. Model installation runs in the background, and the user will be able to check on the install progress in the **Windows Update** page of **Windows Settings**.
* The **MakeAvailableAsync** method has a status option, which can show a loading UI. If the user has unsupported hardware, then **MakeAvailableAsync** will fail with an error.
* Once the model is available, you can call **CreateAsync** to create a new instance from a class that belongs to the model. In your app, you can then use the APIs that belong to that class.

### 4. Next steps: advanced tutorials and APIs

Now that you've successfully checked for model availability, you can dive into the APIs further.

* [Learn more about Windows Copilot Runtime APIs](./api-explained.md)
* [Jump into a more advanced tutorial using OCR APIs](./tutorial.md)

### Troubleshooting

If you run into issues, then see [Troubleshooting and FAQ](./troubleshooting.md).

### Related content

* [Developing responsible generative AI apps and features on Windows](../rai.md)
* [API reference for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
* [Windows App SDK](/windows/apps/windows-app-sdk/)
* [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
