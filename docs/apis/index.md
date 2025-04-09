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

# What are Windows AI APIs?

**Windows AI Foundry** provides a variety of AI-powered features available via APIs, allowing you to utilize AI capabilities without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows AI Foundry on Copilot+ PCs run locally and continuously in the background. When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

# Start Building an App with Windows AI APIs

To use Windows AI APIs, you will first need to ensure that your PC is set up correctly. Follow these steps:

## 1. Prerequisites

First, let's make sure your PC supports WCR APIs. Then, let's install the dependencies you need. 
1. You can do this easily via the WinGet (Windows Package Manager) Configuration file or 
2. You can check and install dependencies manually. 

Both options are detailed below:

### Automated Dependency Check and Installation

1. Instead of doing this manually, simply download this WinGet Configuration File and run it. Download the configuration file from GitHub (click the "..." and download in the top right):

    > [!div class="button"]
    > [Windows AI API Configuration File](https://github.com/hamza-usmani/winget-dsc/blob/wcr-docs-squad/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/configuration.winget)

    This file will:
    - Check for minimum OS version
    - Enable developer mode
    - Install Visual Studio Community with the WinUI and required workloads
    - Install Windows App SDK

2. Next, launch Terminal and navigate to the directory of the downloaded file. Now run the file via the following command:

```shell
   winget configure configuration.winget
```

### Manually Check and Install Dependencies

- Check that your PC is a Copilot+ PC [on this list](/windows/ai/npu-devices/)
- Your Windows version must be [Windows 11 Insider Preview Build 26120.3073 (Dev and Beta Channels)](https://blogs.windows.com/windows-insider/2025/01/31/announcing-windows-11-insider-preview-build-26120-3073-dev-and-beta-channels/) or later on your device. You can run ``winver`` in Windows Search or Run to check your OS version.
- Enable Developer Mode in Settings > System > For Developers > Developer Mode
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- While installing Visual Studio, you need to install the following workloads and components required for developing with WinUI and the Windows App SDK. Follow the [steps here](https://learn.microsoft.com/en-us/windows/apps/get-started/start-here?tabs=vs-2022-17-10#22-required-workloads-and-components). 

<!-- ## Check if your PC is correctly configured

Verify that your PC is correctly setup to use the Windows AI APIs by trying one of the samples in [AI Dev Gallery](../ai-dev-gallery/index.md).

1. Download [AI Dev Gallery](https://apps.microsoft.com/detail/9N9PN1MM3BD5) (or clone the project from [GitHub](https://github.com/microsoft/ai-dev-gallery))
2. If cloned, open the .sln and set the build configuration to `ARM64` and hit F5.
2. In AI Dev Gallery, select the **WCR API tab** menu item then select the *Phi Silica* sample.
3. If the model is already available on your device, the sample should run straight away. If not, select on *request model* to download the model. Once downloaded, the sample will be activated. -->

## 2. Start building a new app

To build your own app that utilizes Windows AI APIs, follow the instructions of your preferred framework below:

### [WinUI](#tab/winui)

1. Open Visual Studio
2. Create a new WinUI project by selecting the **Blank App, Packaged (WinUI 3 in Desktop)** template.

:::image type="content" source="../images/winui-template.png" alt-text="A screenshot of the Visual Studio new Project UI with the WinUI template selected.":::

3. Right-click the project node (your project name/.csproj), click **Properties**. Under Application > General ensure that the target framework is set to .NET 8.0 and the target OS is set to *10.0.22621 or later*.

:::image type="content" source="../images/winui-project-properties-pane.png" alt-text="A screenshot of the Visual Studio project properties pane":::

4. Right-click on your project, and select 'Manage NuGet Packages..'. Check the **Include prelease** checkbox and select Windows App SDK version `1.7.250127003-experimental3`. Click Install or Update. 

<!-- image of nuget package manager + experimental dropdown -->

5. Ensure that your build configuration is set to `ARM64`.

<!-- image of ARM build configuration -->

6. Build and run your app.
7. If the app launches succesfully, continue to step 3 to add the LanguageModel API.

### [WinForms](#tab/winforms)

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

### [Unpackaged console app](#tab/console)

To create an unpackaged app:

1. Open [Visual Studio](https://visualstudio.microsoft.com/downloads/)
2. Create an unpackaged C# console app project, by selecting the **Console App** template.
2. Ensure that your build configuration is set to `ARM64`.
3. Add `<WindowsPackageType>None</WindowsPackageType>` in your project file to declare it as unpackaged.
4. [Install Windows app runtime](/windows/apps/windows-app-sdk/downloads#experimental-release). 

To learn more, see [Tutorial: Build and deploy an unpackaged app using Preview and Experimental channels of the Windows App SDK](/windows/apps/windows-app-sdk/preview-experimental-unpackaged-tutorial?tabs=csharp-dotnet-preview3).

---

## 3. Add your first AI API

When implementing an AI feature using Windows AI APIs, the app should first check for the availability of the AI model supporting that feature. Add the following code to check for model availability, and to generate a response:

### [WinUI](#tab/winui)
Add the following namespace to the top of your ``MainWindow.xaml.cs`` file.
```csharp
using Microsoft.Windows.AI.Generative; 
```

Replace the MainWindow class with the following in ``MainWindow.xaml.cs``.
```csharp
public sealed partial class MainWindow : Window
{
    private static string _result = "result";

    public MainWindow()
    {
        this.InitializeComponent();
        initAI();
    }

    # initAI calles the Language Model API for the formula of glucose, returning it in _result 
    public async void initAI()
    {
        if (!LanguageModel.IsAvailable())
        {
            var op = await LanguageModel.MakeAvailableAsync();
        }

        using LanguageModel languageModel = await LanguageModel.CreateAsync();

        string prompt = "Provide the molecular formula for glucose.";
        myButton.Content = prompt;
        var result = await languageModel.GenerateResponseAsync(prompt);
        _result = result.Response;
    }

    private void myButton_Click(object sender, RoutedEventArgs e)
    {
        myButton.Content = _result;
    }
}
```

If you see the formula for glucose when the button is clicked, congratulations! 

If you run into any errors, it may be because of your hardware or lack of model availability:
- `IsAvailable()` checks if the model required by an AI feature is available on the user's device. This method will return `true` if the model being called is installed on the user's device. This method needs to be called before every call to the model.
- If the model is not available on the user's device, the method `MakeAvailableAsync()` can be called to install the required model. The model installation will run in the background, and the user will be able to check on the install progress in the Windows Update page of the Settings application.
- The `MakeAvailableAsync()` method has a status option which can show a loading UI. If the user has unsupported hardware, `MakeAvailableAsync()` will fail with an error.
- Once the model is available, `CreateAsync()` can be called to create a new instance from a class that belongs to the model. The APIs that belong to that class can then be used in the app

### [WinForms](#tab/winforms)

Add the following namespace to the top of your ``Form1.cs`` file.
```csharp
using Microsoft.Windows.AI.Generative; 
```

Create a button - TODO

Have the button onclick call the languagemodel api and output the response - TODO

---

## 4. Next Steps: Advanced Tutorials and APIs

Now that you've succeeded checking for model availability, dive into the APIs further!

> [!div class="button"]
> [Learn more about WCR APIs](./api-explained.md)

> [!div class="button"]
> [Jump into a more advanced tutorial using OCR APIs](./tutorial.md)

## Troubleshooting
If you run into issues, please see our Frequently Asked Questions and Issues page: [Troubleshooting and FAQ](./troubleshooting.md)

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
