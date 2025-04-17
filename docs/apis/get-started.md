---
title: Start Building an App with Windows AI APIs
description: Learn how to set up your development environment to build Windows AI APIs and check for model availability.
ms.topic: article
ms.date: 04/08/2025
ms.author: mattwoj
author: mousma
reviewer: raamleka
dev_langs:
- csharp
- cpp
---

## Start Building an App with Windows AI APIs

To use Windows AI APIs, you will first need to ensure that your PC is set up correctly. Follow these steps:

### 1. Prerequisites

First, let's make sure your PC supports Windows AI APIs. Then, let's install the dependencies you need.

1. You can do this automated via the WinGet (Windows Package Manager) Configuration file
2. **Or** you can check and install dependencies manually.

Both options are detailed below.

#### [Automated dependency installation (recommended)](#tab/winget)

1. Instead of doing this manually, simply download this WinGet Configuration File and run it. Download the configuration file from GitHub (click the "..." and download in the top right):

    > [!div class="button"]
    > [Windows AI API configuration file](https://github.com/hamza-usmani/winget-dsc/blob/wcr-docs-squad/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/configuration.winget)

    This file will:
    - Check for minimum OS version
    - Enable developer mode
    - Install Visual Studio Community with the WinUI and required workloads
    - Install Windows App SDK

2. Next, either **double click the file** to open it with Windows Package Manager Client.

    Alternatively, launch Terminal and navigate to the directory of the downloaded configuration file. Now run the file via the following command:

    ```shell
    winget configure configuration.winget

#### [Manually install dependencies](#tab/manual)

- Check that your PC is a Copilot+ PC [on this list](/windows/ai/npu-devices/)
- Your Windows version must be [Windows 11 Insider Preview Build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/) or later on your device. You can run ``winver`` in Windows Search or Run to check your OS version.
- Enable Developer Mode in Settings > System > For Developers > Developer Mode
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- While installing Visual Studio, you need to install the following workloads and components required for developing with WinUI and the Windows App SDK. Follow the [steps here](https://learn.microsoft.com/en-us/windows/apps/get-started/start-here?tabs=vs-2022-17-10#22-required-workloads-and-components).

---

### 2. Start building a new app

To build your own app that utilizes Windows AI APIs, follow the instructions of your preferred framework below:

#### [WinUI](#tab/winui)

1. Open Visual Studio
2. Create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.

:::image type="content" source="../images/winui-template.png" alt-text="A screenshot of the Visual Studio new Project UI with the WinUI template selected.":::

3. Right-click the project node (your project name/.csproj), click **Properties**. Under Application > General ensure that the target framework is set to .NET 8.0 and the target OS is set to *10.0.22621 or later*.

:::image type="content" source="../images/winui-project-properties-pane.png" alt-text="A screenshot of the Visual Studio project properties pane":::

4. Right-click on your project, and select 'Manage NuGet Packages..'. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`. Click Install or Update. 

:::image type="content" source="../images/winui-wasdk.png" alt-text="A screenshot of the Visual Studio nuget package with WASDK experimental 1.7.":::

5. Ensure that your build configuration is set to `ARM64`.

:::image type="content" source="../images/winui-arm64.png" alt-text="A screenshot of the Visual Studio build config set to ARM64":::

6. Build and run your app.
7. If the app launches succesfully, continue to step 3 to add the LanguageModel API.


#### [WPF](#tab/wpf)

1. Open Visual Studio
2. Create a new WPF project by selecting the **WPF Application** template.
3. Right click the project node (your project name) in the Solution Explorer and select **Edit Project File** to open the XML. Replace everything inside ``<PropertyGroup>`` with the following:

    ```xml
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
    <WindowsPackageType>None</WindowsPackageType>
    ```

4. In **Solution Explorer**, right-click the **Dependencies** node of your project, and choose **Manage Nuget Packages...**.
5. In the **NuGet Package Manager** window, select the **Browse** tab. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`. Click Install or Update. 
6. Build and run the app.
7. If the app launches succesfully, continue to step 3 to add the LanguageModel API.

To learn more about adding Windows App SDK to a WPF app, check out the documentation [here](https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/wpf-plus-winappsdk#configure-your-wpf-project-for-windows-app-sdk-support).


#### [WinForms](#tab/winforms)

1. Open Visual Studio
2. Create a new WinForms project by selecting the **Windows Forms App** template.
3. Right click the project node (your project name) in the Solution Explorer and select **Edit Project File** to open the XML. Replace everything inside ``<PropertyGroup>`` with the following:

    ```xml
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows10.0.22621.0</TargetFramework>
    <RuntimeIdentifiers>win-x86;win-x64;win-arm64</RuntimeIdentifiers>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
    <WindowsPackageType>None</WindowsPackageType>
    ```

4. In **Solution Explorer**, right-click the **Dependencies** node of your project, and choose **Manage Nuget Packages...**.
5. In the **NuGet Package Manager** window, select the **Browse** tab. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`. Click Install or Update. 
6. Build and run the app.
7. If the app launches succesfully, continue to step 3 to add the LanguageModel API.

To learn more about adding Windows App SDK to a WinForms app, check out the documentation [here](https://learn.microsoft.com/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/winforms-plus-winappsdk#configure-your-winforms-project-for-windows-app-sdk-support).

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

To create an unpackaged app:

1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/)
2. Create an unpackaged C# console app project, by selecting the **Console App** template.
3. Ensure that your build configuration is set to `ARM64`.
4. Add `<WindowsPackageType>None</WindowsPackageType>` in your project file to declare it as unpackaged.
5. [Install Windows app runtime](/windows/apps/windows-app-sdk/downloads#experimental-release). 

To learn more, see [Tutorial: Build and deploy an unpackaged app using Preview and Experimental channels of the Windows App SDK](/windows/apps/windows-app-sdk/preview-experimental-unpackaged-tutorial?tabs=csharp-dotnet-preview3).

---

### 3. Add your first AI API

When implementing an AI feature using Windows AI APIs, the app should first check for the availability of the AI model supporting that feature. Add the following code to check for model availability, and to generate a response:

#### [WinUI](#tab/winui2)

Add the following `TextBlock` in ``MainWindow.xaml``:

```xml
<TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
```

Add the following namespace to the top of your ``MainWindow.xaml.cs`` file.

```csharp
using Microsoft.Windows.AI.Generative; 
```

Replace the MainWindow class with the following in ``MainWindow.xaml.cs``.

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

If the formula for glucose appears in the textblock, congratulations! 

#### [WPF](#tab/wpf2)

Add the following `TextBlock` in ``MainWindow.xaml``:

```xml
<TextBlock x:Name="OutputText" HorizontalAlignment="Center" VerticalAlignment="Center" />
```

Add the following namespace to the top of your ``MainWindow.xaml.cs`` file.

```csharp
using Microsoft.Windows.AI.Generative; 
```

Replace the MainWindow class with the following in ``MainWindow.xaml.cs``.

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

If the formula for glucose appears in the textblock, congratulations! 

#### [WinForms](#tab/winforms2)

In the Designer, drag a `Label` on the page and rename it to `OutputLabel`.

Add the following namespace to the top of your ``Form1.cs`` file.

```csharp
using Microsoft.Windows.AI.Generative; 
```

Replace the Form class with the following in ``Form.cs``.

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

If the formula for glucose appears in the label, congratulations! 

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

If you run into any errors, it may be because of your hardware or lack of model availability:

- `IsAvailable()` checks if the model required by an AI feature is available on the user's device. This method will return `true` if the model being called is installed on the user's device. This method needs to be called before every call to the model.
- If the model is not available on the user's device, the method `MakeAvailableAsync()` can be called to install the required model. The model installation will run in the background, and the user will be able to check on the install progress in the Windows Update page of the Settings application.
- The `MakeAvailableAsync()` method has a status option which can show a loading UI. If the user has unsupported hardware, `MakeAvailableAsync()` will fail with an error.
- Once the model is available, `CreateAsync()` can be called to create a new instance from a class that belongs to the model. The APIs that belong to that class can then be used in the app.

### 4. Next steps: advanced tutorials and APIs

Now that you've succeeded checking for model availability, dive into the APIs further!

> [!div class="button"]
> [Learn more about Windows AI APIs](./api-explained.md)
> [!div class="button"]
> [Jump into a more advanced tutorial using OCR APIs](./tutorial.md)

### Troubleshooting

If you run into issues, please see our Frequently Asked Questions and Issues page: [Troubleshooting and FAQ](./troubleshooting.md)

### Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
