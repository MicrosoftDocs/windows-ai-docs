---
title: Manually implement IActionProvider
description: Learn how to create a provider app for App Actions on Windows by manually implementing the IActionProvider interface.
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---
# Manually implement IActionProvider

This article describes the steps for creating a provider app for App Actions on Windows by manually implementing the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface. Microsoft provides the [Microsoft.AI.Actions NuGet Package](https://www.nuget.org/packages/Microsoft.AI.Actions) which automatically generates the **IActionProvider** implementation based on .NET attributes in your code, allowing you to make strongly typed classes to represent your actions. This is the recommended way of implementing an app action provider app. The guidance in this article is provided to enable edge-case scenarios where developers want to implement **IActionProvider** directly. In this scenario, the Microsoft.AI.Actions Nuget package is still used to auto-generate the COM server activation code required for app action providers.

You can also implement an app action provider using URI launch activation rather than COM activation, although some more advanced scenarios, like streaming text responses from an action, aren't supported. For more information, see [Implement URI launch for App Actions on Windows](actions-uri-launch.md).

App actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. For more information about App Actions on Windows, see [App Actions on Windows Overview](index.md)



#### [Automated dependency installation (recommended)](#tab/winget)

1. Run one of the commands below in Terminal (whether you are a C# or C++ developer). This runs a [WinGet Configuration file](https://github.com/microsoft/winget-dsc/blob/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI) that performs the following tasks (dependencies already installed will be skipped):

    - Enables Developer Mode.
    - Installs Visual Studio Community Edition
    - Include Windows App development workload and either C++ or .NET/C# Workloads
    - Include MSIX Packaging tools

For C# developers:
```console
winget configure https://raw.githubusercontent.com/microsoft/winget-dsc/refs/heads/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/app_actions_cs.winget
```

For C++ developers:
```console
winget configure https://raw.githubusercontent.com/microsoft/winget-dsc/refs/heads/main/samples/Configuration%20files/Learn%20tutorials/Windows%20AI/app_actions_cpp.winget
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
1. Name the new project "ExampleActionProvider".
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". For **Target OS version** and **Supported OS version**, select version 10.0.26100.0 or greater.
1. To update your project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.75</WindowsSdkPackageVersion>
    ```

## Add an action definition JSON file

Action provider apps must provide an action definition file that defines the actions the app implements. This file provides information about the inputs and outputs of your actions and metadata such as a unique identifier and a description for your actions. For more information about the App Action JSON file format, see [Action definition JSON schema for Windows App Action providers](actions-json.md).

This example will define one action called **SendMessage**, that takes a single **Text** entity as input, and returns a single **TextEntity** as output. In addition to defining actions, the JSON file also specifies whether the action provider app should be launched using COM activation or via URI launch. This example will use COM activation.

1. In **Solution Explorer**, right-click the Assets folder and select **Add->New Item...**. 
1. In the **Add New Item** dialog, select **Text File**. Name the new file "registration.json", and click OK.
1. Add the JSON action definition shown in the code example below to the registration.json file.
1. In **Solution Explorer**, right-click the registration.json file and select **Properties**. In the **Properties** pane, set **Build Action** to "Content" and set **Copy to Output Directory** to "Copy if Newer".
1. Replace the **invocation.clsid** value with a new GUID that will identify the provider. You can generate a new GUID in Visual Studio by going to **Tools->Create GUID**. This GUID will be used again in a few different places in this walkthrough.



```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleActionProvider.MyActionProvider.SendMessage",
      "description": "Send a message",
      "icon": "ms-resource://Files/Assets/StoreLogo.png",
      "allowedAppInvokers" : ["*"],
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

## Add an ActionProvider class to handle action operations

This example in this article manually implements the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface. This interface requires the implementation of a single method, [InvokeAsync](/uwp/api/windows.ai.actions.provider.iactionprovider.invokeasync), which the system uses to invoke an action.

1. In **Solution Explorer**, right-click the project icon and select **Add->Class**.
2. In the **Add class** dialog, name the class "MyAppActionProvider" and click **Add**. 
3. In the generated ActionProvider.cs file, update the class definition to indicate that it implements the **IActionProvider** interface.
4. Label the class with the [System.Runtime.InteropServices.GuidAttribute](/dotnet/api/system.runtime.interopservices.guidattribute). This is used by the COM activation code shown later in this walkthrough. Be sure to update the value to the value specified in the **invocation.clsid** field in the registration.json file.
5. Update the class declaration to indicate that inherits the **IActionProvider** interface.


```csharp
// MyAppActionProvider.cs
[System.Runtime.InteropServices.GuidAttribute("00001111-aaaa-2222-bbbb-3333cccc4444")] 
public partial class MyAppActionProvider : IActionProvider
```

## Implement IActionProvider.InvokeAsync

The **InvokeAsync** method has a return type of [IAsyncAction](/uwp/api/windows.foundation.iasyncaction). This example uses a helper class that returns a [Task](/dotnet/api/system.threading.tasks.task), which is then converted to an **IAsyncAction** with a call to **AsAsyncAction** extension method. Add the following method definition to the **MyAppActionProvider** class.

```csharp
// MyAppActionProvider.cs
public IAsyncAction InvokeAsync(ActionInvocationContext context)
{
    return InvokeAsyncHelper(context).AsAsyncAction();
}
```

In the helper method, **InvokeAsyncHelper**, the following actions are performed:

- [ActionInvocationContext.GetInputEntities](/uwp/api/windows.ai.actions.actioninvocationcontext.getinputentities) is called to retrieve the set of entities that are being passed as input into the action.
- An action provider may support multiple actions, so before processing the input values, the [ActionInvocationContext.ActionId](/uwp/api/windows.ai.actions.actioninvocationcontext.actionid) property is evaluated to determine which action is being invoked. The ID will be the value declared for the action in the **id** field in the reisgration.json file.
- In this example, there is a single input entity of type [TextActionEntity](/uwp/api/windows.ai.actions.textactionentity) named "message". The helper method loops through the inputs and checks for the expected name.
- When the expected input entity name is found, it is cast to the **TextEntity** type, and the message text is retrieved using the [Text](/uwp/api/windows.ai.actions.textactionentity.text) property. At this point, a real-world implementation of an action would take this input message, do some processing on it, and generate a response.
- This example creates a response **TextEntity**, as specified in the **outputs** field in the registration.json file. The entity is created from a hard-coded string and then added as an output by calling to [SetOutputEntity](/uwp/api/windows.ai.actions.actioninvocationcontext.setoutputentity), passing in the output entity name and the **TextEntity** object.

Add the following method definition to the **MyAppActionProvider** class.

```csharp
// MyAppActionProvider.cs
async Task InvokeAsyncHelper(ActionInvocationContext context)
{
    NamedActionEntity[] inputs = context.GetInputEntities();

    var actionId = context.ActionId;
    switch (actionId)
    {
      case "ExampleActionProvider.MyActionProvider.SendMessage":
          foreach (NamedActionEntity inputEntity in inputs)
          {
              if (inputEntity.Name.Equals("message", StringComparison.Ordinal))
              {
                
                TextActionEntity entity = (TextActionEntity)(inputEntity.Entity);
                string message = entity.Text;
                
                // TODO: Process the message and generate a response

                string response = "This is the message response"; 
                TextActionEntity result = context.EntityFactory.CreateTextEntity(response);
                context.SetOutputEntity("response", result);
              }
          }
          break;

      default:
          break;

  }

}
```


## Update the app package manifest file

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, the app must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines the app's actions. For more information on the action provider app package manifest format, see [Windows App Action provider package manifest XML format](actions-provider-manifest.md).

To enable COM activation, use the [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The class ID specified in the [com:Class](/uwp/schemas/appxpackage/uapmanifestschema/element-com-exeserver-class) element in the app package manifest must match the **invocation.clsid** value specified in the Action definition JSON file.

1. Right-click the Package.appxmanifest file and select **View Code**
2. Add the following namespaces to the **Package** element at the root of the file.

```xml
xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
xmlns:com2="http://schemas.microsoft.com/appx/manifest/com/windows10/2"
xmlns:com3="http://schemas.microsoft.com/appx/manifest/com/windows10/3"
```

3. Add the following **Extensions** element inside the **Application** element and after the **VisualElements** element. For information about the action provider manifest extensions, see [App Actions on Windows package manifest XML format](actions-provider-manifest.md). Note that the GUID specified as the **Id** for the **com:Class** element will be updated in a later step.

```xml
<Extensions>
  <com2:Extension Category="windows.comServer">
    <com2:ComServer>
        <com3:ExeServer Executable="ExampleActionProvider.exe" DisplayName="ExampleActionProvider">
            <com:Class Id="00001111-aaaa-2222-bbbb-3333cccc4444" DisplayName="ExampleActionProvider" />
        </com3:ExeServer>
      </com2:ComServer>
    </com2:Extension>
    <uap3:Extension Category="windows.appExtension">
        <uap3:AppExtension Name="com.microsoft.windows.ai.actions" DisplayName="Example App Action Provider" Id="exampleappactionprovider" PublicFolder="Assets">
        <uap3:Properties>
            <Registration xmlns="">registration.json</Registration>
        </uap3:Properties>
    </uap3:AppExtension>
</uap3:Extension>
</Extensions>
```

4. Update the **Id** atttribute of the **com:Class** element to the GUID you created in a previous step.

## Implement a custom Main method

In the default project template, the **Main** method entry point is autogenerated by the compiler. This example will disable the autogeneration of **Main** so that the necessary activation code can be run at startup.

1. In **Solution Explorer**, right-click the project icon and select **Edit Project File**.
2. In the **PropertyGroup** element, add the following child element to disable the auto-generated main function.

```xml
<DefineConstants>$(DefineConstants);DISABLE_XAML_GENERATED_MAIN</DefineConstants>
```

3. In **Solution Explorer**, right-click the project icon and select **Add->New Item**.
4. 

### Add a reference to the Microsoft.AI.Actions nuget package

Although this example shows a manual implementation of **IActionProvider**, we will still use the Microsoft.AI.Actions nuget package to autogenerate the COM server activation code needed for the OS to launch the action provider.

1. In **Solution Explorer**, right-click the project name and select **Manage NuGet Packages...**
1. Make sure you are on the **Browse** tab and search for Microsoft.AI.Actions.
1. Select Microsoft.AI.Actions and click Install.

Next, in **Solution Explorer**, right-click the project icon and select **Add->New Item**. Select **Code File**. Change the file name to "Program.cs" and click **Add**.

In the Program.cs file, we will call two helper methods that auto-generate the COM server activation that allows the system to invoke the action provider. 

```csharp
Microsoft.AI.Actions.Helpers.ActionRuntimeFactory.CreateActionRuntime();
  Microsoft.AI.Actions.Helpers.ActionProviderFactory.RegisterActionProvider<MyAppActionProvider,
      Windows.AI.Actions.Provider.IActionProvider>(new MyAppActionProvider());
```

The rest of the code in the **Main** method in this example is just the boilerplate code to launch a WinUI app. Replace the contents of Program.cs with the following code.

```csharp
namespace ExampleActionProvider;

static void Main(string[] args)
{
    Microsoft.AI.Actions.Helpers.ActionRuntimeFactory.CreateActionRuntime();
    Microsoft.AI.Actions.Helpers.ActionProviderFactory.RegisterActionProvider<MyAppActionProvider,
        Windows.AI.Actions.Provider.IActionProvider>(new MyAppActionProvider());
    global::Microsoft.UI.Xaml.Application.Start((p) =>
    {
        var context = new global::Microsoft.UI.Dispatching.DispatcherQueueSynchronizationContext(global::Microsoft.UI.Dispatching.DispatcherQueue.GetForCurrentThread());
        global::System.Threading.SynchronizationContext.SetSynchronizationContext(context);
        new App();
    });
}
```



## Test the Windows App Action

The App Actions Testing Playground app allows you to validate the registration and functionality of your Windows App Action provider app. For more information on using this tool, see [App Actions Testing Playground](actions-test-tool.md).

