---
title: Get started with App Actions on Windows
description: Learn how to create a provider app for App Actions on Windows.
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---
# Get started with App Actions on Windows

This article describes the steps for creating app actions and describes the components of an App Action provider app. App actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. For more information about App Actions on Windows, see [App Actions on Windows Overview](index.md)

The [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface is the primary interface that app action providers use to communicate with the Windows actions framework. However, Microsoft provides the [Microsoft.AI.Actions NuGet Package](https://www.nuget.org/packages/Microsoft.AI.Actions) which automatically generates the **IActionProvider** implementation based on .NET attributes in your code, allowing you to make strongly typed classes to represent your actions. This is the recommended way of implementing an app action provider app and is the technique described in this article. For some edge-case scenarios, developers may want to implement **IActionProvider** directly. For more information, see [Manually implement IActionProvider](actions-iactionprovider-manual.md).

You can also implement an app action provider using URI launch activation rather than COM activation, although some more advanced scenarios, like streaming text responses from an action, aren't supported. For more information, see [Implement URI launch for App Actions on Windows](actions-uri-launch.md).

#### [Automated dependency installation (recommended)](#tab/winget)

1. Run the command below in Terminal (whether you are a C# or C++ developer). This runs a [WinGet Configuration file](https://github.com/microsoft/winget-dsc/blob/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI) that performs the following tasks (dependencies already installed will be skipped):

    - Enables Developer Mode.
    - Installs Visual Studio Community Edition
    - Include Windows App development workload and either C++ or .NET/C# Workloads
    - Include MSIX Packaging tools


```console
winget configure https://raw.githubusercontent.com/microsoft/winget-dsc/refs/heads/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/app_actions_cs.winget
```



#### [Manual dependency installation](#tab/manual)

- Install Visual Studio 2022 (v17.6+)
  - [Download 2022 Community](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Community&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
  - [Download 2022 Professional](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Professional&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
  - [Download 2022 Enterprise](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=Enterprise&channel=Release&Version=VS2022&source=VSLandingPage&add=Microsoft.VisualStudio.Workload.CoreEditor&add=Microsoft.VisualStudio.Workload.NetCrossPlat;includeRecommended&cid=2302)
- Include C++ workload for C++ or .NET workloads for C# development. 
- Make sure that MSIX Packaging Tools under .NET desktop development is selected 
- Make sure Windows Application Development is selected.
- Make sure Windows UI Application Development is selected.

For more information about managing workloads in Visual Studio, see [Modify Visual Studio workloads, components, and language packs](/visualstudio/install/modify-visual-studio).

---

## Create a new Windows app project in Visual Studio

The App Actions on Windows feature is supported for multiple app frameworks, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "Blank App, Packaged (WinUI 3 in Desktop)" project template.
1. Name the new project "ExampleAppActionProvider".
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". For **Target OS version** and **Supported OS version**, select version 10.0.26100.0 or greater.
1. To update your project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.75</WindowsSdkPackageVersion>
    ```

## Add a reference to the Microsoft.AI.Actions Nuget package

The example in this article uses the code generation features of the Microsoft.AI.Actions Nuget package.

1. In **Solution Explorer**, right-click the project name and select **Manage NuGet Packages...**
1. Make sure you are on the **Browse** tab and search for Microsoft.AI.Actions.
1. Select Microsoft.AI.Actions and click Install.

## Add an ActionProvider class to handle action operations

The following section shows how to implement a custom action provider class that uses .NET attributes from the [Microsoft.AI.Actions.Annotations](/windows/actions-source-generation/api/microsoft.ai.actions.annotations) namespace to identify the components of an action. The Microsoft.AI.Actions NuGet package uses these attributes to automatically generate an an underlying implementation of the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface. This allows you to create strongly typed classes for actions without having to interact directly with the low-level action provider APIs.

1. In Visual Studio, right-click the `ExampleAppActionProvider` project in **Solution Explorer** and select **Add->Class**. 
2. In the **Add class** dialog, name the class "MyActionProvider" and click **Add**.
3. Replace the contents of `MyActionProvider.cs` with the following code.

```csharp
using Microsoft.AI.Actions.Annotations;
using System.Threading.Tasks;
using Windows.AI.Actions;

namespace ExampleAppActionProvider
{
    [ActionProvider]
    public sealed class MyActionsProvider
    {
        [WindowsAction(
            Description = "Send a message to a contact",
            Icon = "ms-resource://Files/Assets/StoreLogo.png",
            FeedbackHandler = nameof(SendMessageFeedback),
            UsesGenerativeAI = false
        )]
        [WindowsActionInputCombination(
            Inputs = ["Contact"],
            Description = "Send message to '${Contact.Text}'"
        )]
        [WindowsActionInputCombination(
            Inputs = ["Contact", "Message"],
            Description = "Send '${Message.Text}' to '${Contact.Text}'"
        )]

        public async Task<SendMessageResult> SendMessage(
            [Entity(Name = "Contact")] string contact,
            [Entity(Name = "Message")] string? message,
            InvocationContext context)
        {
            // Your action logic here
            string result = await ProcessMessageAsync(contact, message);

            return new SendMessageResult
            {
                Text = context.EntityFactory.CreateTextEntity(result)
            };
        }
        
        public Task SendMessageFeedback(ActionFeedback feedback, InvocationContext context)
        {
            // Handle user feedback for the action
            return Task.CompletedTask;
        }

        public record SendMessageResult
        {
            public required TextActionEntity Text { get; init; }
        }

        public async Task<string> ProcessMessageAsync(string contact, string? message)
        {
            if (message != null)
            {
                return await Task.Run(() => $"Processed {contact}, {message}");
            }
            else
            {
                return await Task.Run(() => $"Processed {contact}");
            }
        }
    }
}
```

The code generation features of the Microsoft.AI.Actions Nuget package uses .NET attributes in your code to determine the details of the actions your app provides. This example uses the following attributes:

| Attribute | Description |
|-----------|-------------|
| [ActionProviderAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.actionproviderattribute) | This attribute identifies a class that implements one or more actions. |
| [WindowsActionAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.windowsactionattribute) | This attribute provides metadata about an action, such as the human-readable description of the app and an icon file that consumers of you actions can display to users. |
| [WindowsActionInputCombinationAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.windowsactioninputcombinationattribute) | This attribute declares a set of input entities that an action can accept as input. A single action can support multiple combinations of inputs. |
| [EntityAttribute](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.entityattribute) |Indicates that a class represents an [ActionEntity](/uwp/api/windows.ai.actions.actionentity) |

Most of the supported attributes map directly to fields in the action definition JSON file that the system uses to discover actions. In fact, as will be shown later in this article, the Microsoft.AI.Actions code generation feature uses these attributes to automatically generate the action definition JSON file at build time. As you update your action provider class, adding or modifying these attributes, the Nuget package will regenerate the action definition file to reflect your changes. For more information on the action definition JSON file, see [Action definition JSON schema for App Actions on Windows](actions-json.md).

For a list of the supported attributes see the readme file for the [Microsoft.AI.Actions](https://www.nuget.org/packages/Microsoft.AI.Actions) Nuget package.

## Update the app package manifest file

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, the app must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines the app's actions. You must also specify `com.microsoft.windows.ai.actions` as the **Name** for the **uap3:AppExtension** element. For more information on the action provider app package manifest format, see [Windows App Action provider package manifest XML format](actions-provider-manifest.md).

The example in this walkthrough uses COM activation to launch the app action  provider. To enable COM activation, use the [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The **invocation.clsid** value specified in the Action definition JSON file must match the class ID specified in the [com:Class](/uwp/schemas/appxpackage/uapmanifestschema/element-com-exeserver-class) element in the app package manifest.

1. Right-click the Package.appxmanifest file and select **View Code**
2. Add the following namespaces to the **Package** element at the root of the file.

```xml
xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
xmlns:com2="http://schemas.microsoft.com/appx/manifest/com/windows10/2"
xmlns:com3="http://schemas.microsoft.com/appx/manifest/com/windows10/3"
```

3. Add the following **Extensions** element inside the **Application** element and after the **VisualElements** element. 

```xml
<Extensions>
  <com2:Extension Category="windows.comServer">
    <com2:ComServer>
        <com3:ExeServer Executable="ExampleAppActionProvider.exe" DisplayName="ExampleAppActionProvider">
            <com:Class Id="00001111-aaaa-2222-bbbb-3333cccc4444" DisplayName="ExampleAppActionProvider" />
        </com3:ExeServer>
      </com2:ComServer>
    </com2:Extension>
    <uap3:Extension Category="windows.appExtension">
        <uap3:AppExtension Name="com.microsoft.windows.ai.actions" DisplayName="Example App Action Provider" Id="appactionprovider" PublicFolder="Assets">
        <uap3:Properties>
            <Registration>registration.json</Registration>
        </uap3:Properties>
    </uap3:AppExtension>
</uap3:Extension>
</Extensions>
```

## Implement a custom Main method

In the default project template, the **Main** method entry point is autogenerated by the compiler. This example will disable the autogeneration of **Main** so that the necessary activation code can be run at startup.

1. In **Solution Explorer**, right-click the project icon and select **Edit Project File**.
1. In the **PropertyGroup** element, add the following child element to disable the auto-generated main function.

```xml
<DefineConstants>$(DefineConstants);DISABLE_XAML_GENERATED_MAIN</DefineConstants>
```

Next, in **Solution Explorer**, right-click the project icon and select **Add->New Item**. Select **Code File**. Change the file name to "Program.cs" and click **Add**.

In the Program.cs file, we will add a line of code that will cause the actions nuget package to auto-generate the COM server activation that allows the system to invoke the action provider.

```csharp
ComServerRegisterActions.RegisterActions();
```

The rest of the code in the **Main** method in this example is just the boilerplate code to launch a WinUI app. Replace the contents of Program.cs with the following code.

```csharp
namespace ExampleAppActionProvider;

public static class Program
{

    [global::System.STAThreadAttribute]
    static void Main(string[] args)
    {
        global::WinRT.ComWrappersSupport.InitializeComWrappers();
        ComServerRegisterActions.RegisterActions();
        global::Microsoft.UI.Xaml.Application.Start((p) =>
        {
            var context = new global::Microsoft.UI.Dispatching.DispatcherQueueSynchronizationContext(global::Microsoft.UI.Dispatching.DispatcherQueue.GetForCurrentThread());
            global::System.Threading.SynchronizationContext.SetSynchronizationContext(context);
            new App();
        });
    }
}
```

## Add Microsoft.AI.Actions configuration properties to the project file

The code generation feature of the Microsoft.AI.Actions Nuget package uses property values defined in the project file to configure its behavior at build time. Add the following properties inside the first **PropertyGroup** element in your .csproj file.


```xml
<GenerateActionRegistrationManifest>true</GenerateActionRegistrationManifest>
<ActionRegistrationManifest>Assets\registration.json</ActionRegistrationManifest>
<GenerateActionsWinRTComServer>true</GenerateActionsWinRTComServer>
<RootNamespace>ExampleAppActionProvider</RootNamespace>
```

The following table describes these properties.

| Property | Description |
|----------|-------------|
| GenerateActionRegistrationManifest | When set to **true** the actions package will auto-generate an action definition JSON file based on the .NET attributes in your action provider class definition. Note that manual changes you make to the generated action definition file will be overwritten whenever you build the project. So, if you need to preserve manual changes you have made, you can set this value to **false**. |
| ActionRegistrationManifest | The package-relative path to the auto-generated action definition JSON file. Note that the system will look in the folder specified in the **PublicFolder** attribute of the **uap3:AppExtension** element in the app package manifest file. So make sure that path for this property and the public folder declared in the manifest file match. |
| GenerateActionsWinRTComServer | Set this to **true** to enable autogeneration of COM Server activation code from the **ComServerRegisterActions.RegisterActions** call in `Program.cs` shown previously in this article. If this value is set to **false** you will need to implement your own COM Server activation.|
| RootNamespace | Sets the root namespace of the auto-generated code so that you can access it from your own code. |


## Make updates based on the generated registration.json file

After building your project you can view the generated `registration.json` file in the **Assets** folder in **Solution Explorer**.  


```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleAppActionProvider.MyActionsProvider.SendMessage",
      "description": "Send a message to a contact",
      "icon": "ms-resource://Files/Assets/StoreLogo.png",
      "usesGenerativeAI": false,
      "hasFeedbackHandler": true,
      "inputs": [
        {
          "name": "Contact",
          "kind": "Text"
        },
        {
          "name": "Message",
          "kind": "Text"
        }
      ],
      "inputCombinations": [
        {
          "inputs": [
            "Contact"
          ],
          "description": "Send message to '${Contact.Text}'"
        },
        {
          "inputs": [
            "Contact",
            "Message"
          ],
          "description": "Send '${Message.Text}' to '${Contact.Text}'"
        }
      ],
      "outputs": [
        {
          "name": "Text",
          "kind": "Text"
        }
      ],
      "invocation": {
        "type": "COM",
        "clsid": "11112222-bbbb-3333-cccc-4444dddd5555"
      }
    }
  ]
}
```

### Update the **CLSID** in the app package manifest file

The first time you build your action provider app, you will get the warning: `warning WASDK0012: The Action Provider type ExampleAppActionProvider.MyActionsProvider is not registering a ComServer with Class Id '00000000-0000-0000-0000-0000000'`. This is because the auto-generated `registration.json` file declares the **clsid** of the COM server for the action with a unique GUID. After building your project, open the `registration.json` file and note that the file declares that the action uses COM activation and specifies a **clsid** value. Replace the value of the **Id** attribute in the **com:Class** element in your app package manifest file to use the generated GUID.

For example, if the **clsid** value in the generated `registration.json` file is `11112222-bbbb-3333-cccc-4444dddd5555`, the updated **com:Class** element would look like the following:

`<com:Class Id="11112222-bbbb-3333-cccc-4444dddd5555" DisplayName="ExampleAppActionProvider" />`

### Allowed app invokers

A new field that has been added to the action definition JSON schema is **allowedAppInvokers** which specifies a list of Application User Model IDs (AppUserModelIDs) that can discover the action through a call to [GetActionsForInputs](/uwp/api/windows.ai.actions.hosting.actioncatalog.getactionsforinputs) or [GetAllActions](/uwp/api/windows.ai.actions.hosting.actioncatalog.getallactions). Wildcards are supported. "\*" will match all AppUserModelIDs. This is recommended for most actions, unless there is a specific reason to limit the callers that can invoke an action.  If **allowedAppInvokers** is omitted or is an empty list, no apps will be able to discover the action. For more information on AppUserModelIDs, see [Application User Model IDs](/windows/win32/shell/appids).

The following example shows the recommended practice of setting **allowedAppInvokers** to allow all apps to discover the associated action.

```json
"actions": [
    {
      "id": "ExampleAppActionProvider.MyActionsProvider.SendMessage",
      "description": "Send a message to a contact",
      "icon": "ms-resource://Files/Assets/StoreLogo.png",
      "usesGenerativeAI": false,
      "hasFeedbackHandler": true,
      "allowedAppInvokers" : ["*"],
    ...
```

> [!IMPORTANT]
> In the current **Microsoft.AI.Actions** release, **allowedAppInvokers** will be overwritten whenever the action definition file is regenerated. After adding **allowedAppInvokers** to your action definition JSON file, you should set **GenerateActionRegistrationManifest** to **false** in your project file. If you modify your code and need to enable the JSON file generation again, be sure to add **allowedAppInvokers** back to the file and disable JSON file generation again.

## Test the Windows App Action

The App Actions Testing Playground app allows you to validate the registration and functionality of your Windows App Action provider app. For more information on using this tool, see [App Actions Testing Playground](actions-test-tool.md).