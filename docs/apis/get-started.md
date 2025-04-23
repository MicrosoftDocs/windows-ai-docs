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

This guide describes both hardware requirements and how to configure a device to successfully build apps using the Windows Copilot Runtime APIs.

## Dependencies

Ensure that your PC supports Windows Copilot Runtime and that all dependencies are installed. You can choose to do this automatically (recommended) or manually.

#### [Automated dependency installation (recommended)](#tab/winget)

1. Download the [Windows Copilot Runtime configuration .winget file](https://github.com/hamza-usmani/winget-dsc/blob/wcr-docs-squad/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/configuration.winget) from the *winget-dsc* GitHub repo (click **...** > **Download** from the top of the page).

1. Next, double-click the file to open it with the Windows Package Manager client (or launch Terminal, navigate to the folder where you downloaded `configuration.winget`, and run the file using `winget configure configuration.winget`).

    This performs the following:
    - Checks for minimum OS version.
    - Enables Developer Mode.
    - Installs Visual Studio Community Edition with WinUI and other required workloads.
    - Installs the Windows App SDK.

#### [Manual dependency installation](#tab/manual)

- Confirm that your PC is a Copilot+ PC and is also listed in the [Copilot+ PCs developer duide](/windows/ai/npu-devices/).
- You must have [Windows 11 Insider Preview build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/), or later installed. To check your OS version, run `winver` in Windows Search.
- Enable Developer Mode in **Settings** > **System** > **For developers** > **Developer Mode**.
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- While installing Visual Studio, you need to install specific workloads and components for developing with WinUI and the Windows App SDK. Follow the steps in [Required workloads and components](/windows/apps/get-started/start-here#22-required-workloads-and-components).

---

## Start building a new app

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

#### [.NET MAUI](#tab/maui)
To build a MAUI app using WCR APIs, you will need to: 

1. Create a MAUI project 
If you are starting with a new MAUI project, create an app using the steps at [Build your first .NET MAUI app](/dotnet/maui/get-started/first-app.md). 
2. Add a reference to the version of the Microsoft.WindowsAppSDK package containing WCR. 
  * Go to the Solution Explorer, then select Project > Edit Project File 
![Screenshot of Solution Explorer.](../images/GetImage(0).png)

  * At the bottom of the project file, add these lines to reference the correct Microsoft.WindowsAppSDK package version conditioned only when compiling for the Windows platform: 
``` 

  <ItemGroup Condition="$([MSBuild]::GetTargetPlatformIdentifier('$(TargetFramework)')) == 'windows'"> 

    <PackageReference Include="Microsoft.WindowsAppSDK" Version="1.7.250127003-experimental3"/> 

  </ItemGroup> 

```
![Screenshot of Item Group.](../images/GetImage(1).png)

Note: While the Project > Manage NuGet Packages option can be used to add the required package, the project file still needs to be edited to condition the package reference for just Windows builds if your app is also building for other platforms (like Android and iOS). 

 

3. Update the project to set the target framework for Windows to 22621 or later. 

  * Go to the Solution Explorer, then select Project > Properties 

  * Scroll down to Windows Targets and set the Target Windows Framework to 10.0.22621.0 (or later)
![Screenshot of Windows Targets.](../images/GetImage(2).png)

4. In the Visual Studio toolbar, press the Windows Machine button to build and run the app:
![Screenshot of Windows Machine.](../images/GetImage(3).png)

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

#### [.NET MAUI](#tab/maui2)
Now that the basic app is working and configured so it can use WCR, it is time to call the first WCR API. See [.NET MAUI: Invoke platform code](/dotnet/maui/platform-integration/invoke-platform-code.md) for details on adding platform-specific code to a MAUI app. Let's use the partial classes and partial methods approach to put the Windows code in the Platform\Windows folder. 
  * In MainPage.xaml.cs, add a partial method definition as `partial void ChangeLanguageModelAvailability();` and call that partial method at the beginning of the OnCounterClicked method.
![Screenshot of OnCounterClicked.](../images/GetImage(4).png)

  * In the Solution Explorer, expand Platforms, right-click on Windows, select Add > Class…, type the name MainPage.cs, and click Add.
![Screenshot of Add New Item.](../images/GetImage(5).png)

  * The new MainPage.cs should be shown in the editor window. Switch back to MainPage.xaml.cs to copy its namespace line.
![Screenshot of Namespace.](../images/GetImage(6).png)

  * Switch back to the new MainPage.cs and replace its namespace line with the line from MainPage.xaml.cs. This is to make the Platform\Windows class a partial extension of the base MainPage class. 

  * To complete making MainPage.cs be an extension, replace "internal" on the class declaration with "partial".
![Screenshot of MainPage.cs.](../images/GetImage(7).png)

  * Add a definition of the partial ChangeLanguageModelAvailability method defined earlier: 
``` 

        partial void ChangeLanguageModelAvailability() 
        { 
            try 
            { 
                bool isAvailable = Microsoft.Windows.AI.Generative.LanguageModel.IsAvailable(); 
                System.Diagnostics.Debug.WriteLine($"LanguageModel.IsAvailable: {isAvailable}"); 
            } 
            catch (Exception e) 
            { 
                System.Diagnostics.Debug.WriteLine($"LanguageModel is not available: {e}"); 
            } 
        } 
```
![Screenshot of add definition.](../images/GetImage(8).png)

6. Run the app again, click the "Click me" button, and observe the output in the Visual Studio debug output pane. 

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
