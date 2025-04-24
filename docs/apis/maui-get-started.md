---
title: WCR APIs Tutorial
description: Learn and try building apps that use WCR APIs!
ms.topic: article
ms.date: 02/06/2025
dev_langs:
- csharp
- cpp
---

## Build a .NET MAUI App with Windows Copilot Runtime APIs

Make sure you're familiar with the basic dependencies and knowledge for [.net MAUI](https://learn.microsoft.com/en-us/dotnet/maui/get-started/first-app?view=net-maui-9.0&tabs=vswin&pivots=devices-windows)

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

5. Now that the basic app is working and configured so it can use WCR, it is time to call the first WCR API. See [.NET MAUI: Invoke platform code](/dotnet/maui/platform-integration/invoke-platform-code.md) for details on adding platform-specific code to a MAUI app. Let's use the partial classes and partial methods approach to put the Windows code in the Platform\Windows folder. 
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

For more complete use of WCR APIs, check out the various "Get started" links for the APIs at [Windows Copilot Runtime APIs Overview](/windows/ai/apis/). 

#### Troubleshooting 
Here is a list of commonly found bugs: 
* In step 4, you may find that the Visual Studio toolbar has switched from "Windows Machine" to "Android Emulator" as the debug target. Switching to "Windows Machine" may also automatically switch back to "Android Emulator" after a few seconds. To fix this, you can either unload and reload the project, or close and restart Visual Studio. 
* In step 6, if you hit the exception then your PC may either be unsupported or not correctly configured. Verify the prerequisites and PC configuration as described on the [Get started](/windows/ai/apis/model-setup#prerequisites) page. 

#### Sample project 
For a complete MAUI sample, check out [MauiWindowsCopilotRuntimeSample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsCopilotRuntime/cs-maui) 

## Debugging
If you're running into issues with your app, make sure to check out our Troubleshooting page here [link]
If you'd like to file feedback, please use the following steps:
1. Open Feedback Hub (Use Windows key + F)
2. Make sure you're signed in to Feedback Hub.
3. In section 1, Start entering Bug details. Please prefix your bug title with "[WCR APIs]".
4. In section 2, use Developer Platform -> Windows Copilot Runtime
5. In section 4, Add more details -> Recreate my problem
6. Start recording, run your test app/the samples below, stop recording
7. Please fill in as much info as possible, including repro steps.
8. Submit the feedback

For feature requests, please submit an Issue on our GitHub community [link]

## Next steps
Congratulations! You’ve successfully built an app that uses WCR APIs. If you’d like to publish your app, [have a look here.](https://learn.microsoft.com/en-us/windows/apps/publish/?tabs=individual%2Cmsix-pwa-getting-started)

If you’d like to learn about WCR APIs in more detail, [have a look here.](https://learn.microsoft.com/en-us/windows/ai/apis/)

Want to try things out more before committing? Try out the AI Dev Gallery app! (Store link)
