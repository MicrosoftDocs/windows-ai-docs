---
title: Get started with Windows App Actions
description: Learn how to create Windows App Action provider app and understand the components of an action provider app.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/23/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the Windows App Actions ecosystem.

---


# Get started with Windows App Actions

This article describes the steps for creating a Windows App Action provider and describes the components of an App Action provider app. Windows App Actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. For more information about Windows App Actions, see [Windows App Actions Overview](index.md)


## Prerequisites

- Install Visual Studio 2022 (v17.6+)
  - [Download 2022 Community](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Community&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
  - [Download 2022 Professional](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Professional&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
  - [Download 2022 Enterprise](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Enterprise&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
- Include C++ workload for C++ or .NET workloads for C# development. 
- Make sure that MSIX Packaging Tools under .NET desktop development is selected 
- Make sure Windows Application Development is selected.

For more information about managing workloads in Visual Studio, see [Modify Visual Studio workloads, components, and language packs](/visualstudio/install/modify-visual-studio).

## Install the Windows App Actions VSIX Extension

The Windows App Actions VSIX Extension allows you to add a basic Windows App Action provider implementation to an existing Windows app Visual Studio project.

1. Download the Windows App Actions VSIX Extentsion. [Link TBD]
1. Double click the .vsix file to install the extension. 

## Create a new Windows app project in Visual Studio

The Windows App Actions feature is supported for multiple app frameworks and languages, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "Blank App, Packaged (WinUI 3 in Desktop)" project template.
1. Name the new project "ExampleActionProvider". When prompted, set the target .NET version to 8.0. [TBD - Target .NET version]
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". Under **Target OS Version**, select version [TBD - Target version] or later.
1. [TBD - Is this required for 5D release? ] To update your project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.59-preview</WindowsSdkPackageVersion>
    ```

## Use the App Actions VSIX Extension to add App Action provider components

1. In **Solution Explorer**, right-click the ExampleActionProvider project, right-click on the app project and select **Add->New Item...**.
1. From the list of templates, select **Windows Action Handler** and click **Add**.

## About the App Action provider components

When you add a new **Windows Action Handler** item to an existing project, the extension creates some new files and modifies existing files to add Windows App Action support to the app. This section walks through the changes made to the project to help you understand the components of a basic action provider so that you can update the project to implement your own Windows App Actions.

[TBD - verify these components with final VSIX build]

### registration.json

The registration.json file registers one or more actions with the system. This file provides information about the inputs and outputs of your actions and metadata such as a unique identifier and a description for your actions. For more information about the App Action JSON file format, see [Action definition JSON schema for Windows App Action providers](action-json.md).

Objects that actions operate on are called "Entities", and there is a set of defined entity subtypes that are supported by the system, such as **Text** or **Photo**. The set of supported entity types are provided in the Action definition JSON schema or [TBD - link to relevant API]. The default action created by the App Actions VSIX Extension defines a simple "Hello World" action that takes a single **Text** entity named "message" as an input, and returns a single **Text** entity named "response" as an output.

```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleActionProvider.WindowsActionHandler.SendMessage",
      "description": "Send a message",
      "icon": "ms-resource://Files/Assets/SendMessageActionIcon.png",
      "usesGenerativeAI": false,
      "inputs": [
        {
          "name": "message",
          "kind": "Text"
        }
      ],
      "inputCombinations": [
        {
          "inputs": [
            "message"
          ],
          "description": "Send message '${message.Text}'"
        }
      ],
      "outputs": [
        {
          "name": "response",
          "kind": "Text"
        }
      ],
      "invocation": {
        "type": "COM",
        "clsid": "00001111-aaaa-2222-bbbb-3333cccc4444"
      }
    }
  ]
}
```

### Package.appmanifest

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, you must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines your app's actions. For more information on the action provider app package manifest format, see [Windows App Action provider package manifest XML format](action-provider-manifest.md).

Windows App Actions can choose to be activated by the system using COM activation and/or using URI launch activation. The default action created by the App Actions VSIX Extension uses COM activation. To support this, it places a [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The **invocation.clsid** value specified in the Action definition JSON file must match the class ID specified in the [com:Class](/uwp/schemas/appxpackage/uapmanifestschema/element-com-exeserver-class) element in the app package manifest.

```xml
<Extensions>
  <uap3:Extension Category="windows.appExtension">
    <uap3:AppExtension Name="com.microsoft.windows.ai.actions" DisplayName="ActionsTest1" Id="actionstest1" PublicFolder="Assets">
      <uap3:Properties>
        <Registration xmlns="">registration.json</Registration>
      </uap3:Properties>
    </uap3:AppExtension>
  </uap3:Extension>
  <com2:Extension Category="windows.comServer">
    <com2:ComServer>
      <com3:ExeServer Executable="ActionsTest1.exe" DisplayName="ActionsTest1">
        <com:Class Id="00001111-aaaa-2222-bbbb-3333cccc4444" DisplayName="ActionsTest1" />
      </com3:ExeServer>
    </com2:ComServer>
  </com2:Extension>
</Extensions>
```

### WindowsActionHandler.cs

When a Windows App Action provider uses COM activation, the system initiates a the provider's declared actions by calling the [InokeAsync TBD link target]() method of the [IActionProvider TBD link target]() interface. Instead of implementing this method and parsing the arguments to determine which action is being invoked, which input combination is being passed in, and other information about the requested action, the App Actions VSIX Extension provides code generation APIs that automatically generate the **IActionProvider** code for you, based on attribute annotations that you provide in an action handler class.

The code example below shows the **WindowsActionHandler** class that implements the simple default action handler provided by the App Actions VSIX Extension. The **ActionProvider** attribute on the **WindowsActionHandler** class tells the code generator that the **IActionProvider** interface methods should be generated for this class

The **SendMessage** method represents an invocation of one action that is declared in the Action description JSON file. In the example JSON file show above, note that **id** for the default action matches the namespace, class name, and method name defined in the handler, "ExampleActionProvider.WindowsActionHandler.SendMessage". The **WindowsAction** attribute provides metadata about the method. The same action can be overloaded to except different combinations of inputs. The **InputCombination** attribute specifies which combination of inputs is associated with the method.

[TBD - The current text is based on the default action handler generated by the Add New Item template. I need to understand the scenario where a developer, for example, adds another action to the generated handler, in order to better explain how this attribute-based framework works. For example, if a dev adds a new action with a different result type, do they need to manually add the {NewActionName}Result class using this naming convention?]

For more information about how attributes work in .NET, see [Attributes](/dotnet/csharp/advanced-topics/reflection-and-attributes/).

```csharp
//WindowsActionHandler.cs
using System;
using System.Threading.Tasks;
using Windows.AI.Actions.Annotations;

namespace ExampleActionProvider
{
    [ActionProvider]
    public sealed class WindowsActionHandler
    {
        [WindowsAction(Description = "Send a message", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]
        [InputCombination(Inputs = ["message"], Description = "Send message '${message.Text}'")]
        public async Task<SendMessageResult> SendMessage(string message)
        {
            return new SendMessageResult
            {
                Response = "Hello world"
            };
        }
    }

    public record SendMessageResult
    {
        public required string Response { get; init; }
    }
}
```

### Program.cs

In order to support COM activation, Windows App Action provider apps must implement code to initialize, register, and start a COM server. In order to make this easier, the App Actions VSIX Extension automatically generates the code for these tasks in the Program.cs file that is added to the project. To make the generated code simpler and less verbose, the extension uses the 3rd party Shmuelie.WinRTServer library that provides wrappers for COM server activation. For more information on this library see [https://github.com/shmuelie/Shmuelie.WinRTServer](https://github.com/shmuelie/Shmuelie.WinRTServer).

```csharp
//Program.cs
using System.Threading.Tasks;

namespace ActionsTest1
{
    public static class Program
    {
        [global::System.STAThreadAttribute]
        static async Task Main(string[] args)
        {
            global::WinRT.ComWrappersSupport.InitializeComWrappers();

            await using (global::Shmuelie.WinRTServer.ComServer server = new())
            {
                server.RegisterActions();
                server.Start();

                global::Microsoft.UI.Xaml.Application.Start((p) =>
                {
                    var context = new global::Microsoft.UI.Dispatching.DispatcherQueueSynchronizationContext(global::Microsoft.UI.Dispatching.DispatcherQueue.GetForCurrentThread());
                    global::System.Threading.SynchronizationContext.SetSynchronizationContext(context);
                    new App();
                });
            }
        }
    }
}
```


## Build and deploy your Windows App Action

## Use the [TBD Name] Test Tool to test your Windows App Action

## Additional Windows App Actions tasks

### Actions availability

### Streaming text output from Actions

### Referencing remote files 

### Next step -or- Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

* [Related article title](link.md)
* [Related article title](link.md)
* [Related article title](link.md)

<!-- Optional: Next step or Related content - H2

C