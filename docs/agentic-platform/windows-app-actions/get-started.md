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

## Create a new Windows app project in Visual Studio

The Windows App Actions feature is supported for multiple app frameworks and languages, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "Blank App, Packaged (WinUI 3 in Desktop)" project template.
1. Name the new project "ExampleAppActionProvider". When prompted, set the target .NET version to 8.0. [TBD - Target .NET version]
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". Under **Target OS Version**, select version [TBD - Target version] or later.
1. To update your project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.59-preview</WindowsSdkPackageVersion>
    ```

## Add an action definition JSON file

Action provider apps must provide an action definition file that defines the actions the app implements. This file provides information about the inputs and outputs of your actions and metadata such as a unique identifier and a description for your actions. For more information about the App Action JSON file format, see [Action definition JSON schema for Windows App Action providers](action-json.md).

This example will define one action called **SendMessage**, that takes a single **Text** entity as input, and returns a single **TextEntity** as output. In addition to definiing actions, the JSON file also specifies whether the action provider app should be launched using COM activation or via URI launch. This example will use COM activation.

1. In **Solution Explorer**, right-click the ExampleAppActionProvider project file and select **Add New Item...**. 
1. In the **Add New Item** dialog, select **Text File**. Name the new file "registration.json", and click OK.
1. Add the following JSON action definition to the registration.json file.
1. Replace the **invocation.clsid** value with a new GUID that will identify the provider. You can generate a new GUID in Visual Studio by going to **Tools->Create GUID**. This GUID will be used again in a few different places in this walkthrough.
1. In **Solution Explorer**, right-click the registration.json file and select **Properties**. In the **Properties** pane, set **Build Action** to "Content" and set **Copy to Output Directory** to "Copy if Newer".


```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleAppActionProvider.WindowsActionHandler.SendMessage",
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

## Add a ActionProvider class to handle action operations

In Visual Studio, right-click the `ExampleAppActionProvider` project in **Solution Explorer** and select **Add->Class**. In the **Add class** dialog, name the class "ActionProvider" and click **Add**. In the generated ActionProvider.cs file, update the class definition to indicate that it implements the **IActionProvider** interface.

```csharp
// WidgetProvider.cs
internal class ActionProvider : IActionProvider
```

TBD - waiting for the example app to work

```csharp
public IAsyncAction InvokeAsync(ActionInvocationContext context)
{
    // In order to return IAsyncAction we need to create a helper that can return Task<T> first 
    // We will use .AsAsyncAction() extension method to convert Task<T> to IAsyncOperation<T>. 

    return InvokeAsyncHelper(context).AsAsyncAction();
}
```

```csharp
async Task InvokeAsyncHelper(ActionInvocationContext context)
{
    // Use actionName to invoke the appropriate action. 
    // This name will match one of the action's name you've declared in actions registration.json. 
    string actionName = context.ActionName;



    // context.GetInputEntities returns all the input entities for your action that you've defined in the action registration json.  
    // You should expect only the types of entities that you've defined in the action registration json. 
    NamedActionEntity[] inputs = context.GetInputEntities();


    switch (actionName)
    {

        // As an example, this action takes Region input as text entity and returns its city as text entity 

        case "message":

            foreach (NamedActionEntity inputEntity in inputs)
            {

                if (inputEntity.Name.Equals("message", StringComparison.Ordinal))
                {
                    // We first need to convert generic ActionEntity type to a specific entity action expects as argument.
                    // We know that region is of type TextActionEntity since in action registration for inputs we defined: 
                    // { "name": "message", "kind": "Text } 
                    // Convert the input entity to TextActionEntity: 

                    //TextActionEntity entity = CastToType<ActionEntity, TextActionEntity>(inputEntity.Entity); // Implementation of CastToType is omitted in this example. 
                    TextActionEntity entity = (TextActionEntity)(inputEntity.Entity);
                    string message = entity.Text;
                    string response = "response"; // Implementation is omitted in this example. 



                    // Once we retrieved the city which we will send as result of the action invocation, 
                    // we need to create a TextActionEntity that will represent the result. 
                    // Action Framework will expect a TextActionEntity as result since that's what we declared in the registration.json: 
                    // "outputTypes" : [ { "name" : "response", "kind" : "Text" } ] 

                    TextActionEntity result = context.EntityFactory.CreateTextEntity(response);



                    // Add the result to the context 

                    // "City" is used as key since that's the name of the output defined in action registration 

                    context.SetOutputEntity("response", result);



                    // You can also set context.Result and context.ExtendedError if you are unable to successfully invoke the action 

                }

            }

            break;

        default:

            break;

    }

}
```


## Update the app package manifest file

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, you must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines your app's actions. For more information on the action provider app package manifest format, see [Windows App Action provider package manifest XML format](action-provider-manifest.md).

Windows App Actions can choose to be activated by the system using COM activation and/or using URI launch activation. The default action created by the App Actions VSIX Extension uses COM activation. To support this, it places a [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The **invocation.clsid** value specified in the Action definition JSON file must match the class ID specified in the [com:Class](/uwp/schemas/appxpackage/uapmanifestschema/element-com-exeserver-class) element in the app package manifest.

1. Right-click the Package.appxmanifest file and select **View Code**
2. Add the following namespaces to the **Package** element at the root of the file. TBD - confirm namespaces

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
            <com:Class Id="9C39E918-8907-425C-A24F-9FE30FFF6FE1" DisplayName="ExampleAppActionProvider" />
        </com3:ExeServer>
      </com2:ComServer>
    </com2:Extension>
    <uap3:Extension Category="windows.appExtension">
        <uap3:AppExtension Name="com.microsoft.windows.ai.actions" DisplayName="Example App Action Provider" Id="appactionprovider" PublicFolder="Assets">
        <uap3:Properties>
            <Registration xmlns="">registration.json</Registration>
        </uap3:Properties>
    </uap3:AppExtension>
</uap3:Extension>
</Extensions>
```

## Activation TBD


## Use the [TBD Name] Test Tool to test your Windows App Action

[TBD - Screenshots of the test tool running this action would be nice to have here]

1. In Visual Studio, press F5 to build your Windows App Action provider app.
1. In **Solution Explorer**, right-click the project icon and select **Deploy** to deploy your app, registering your action with the system.
1. Download and install the Windows App Actions Test Tool [TBD - download link].
1. Launch the Windows App Actions Test Tool.
1. On the **Action catalog** tab, click on the entry for your action. If you followed the steps in this walkthrough, the action will be named "ExampleActionProvider.WindowsActionHandler.SendMessage" and the description will be "Send a message"
1. Since the provider app only implements one overload of the send message action, "Send message '${message.Text}' will automatically be selected in the **Overloads**.
1. Under **Inputs**, make sure that "Sample text entity" is selected in in the **message** drop-down.
1. Click the **Run Action** button.
1. You will see your app launch. [TBD - Should we have some best practice guidance for modifying the template to *not* launch the Window?]
1. In the Windows App Actions Test Tool, a modal dialog will launch showing the "Hello world" message response from your action. 

## Additional Windows App Actions scenarios

The following sections list some other scenarios that Windows App Action providers may choose to implement.

### Actions availability

A Windows App Action provider can inform the system of the availability of one or more of its registered actions by calling [ActionRuntime.SetActionAvailability- TBD link needed](). This feature enables scenarios such as requiring a login or subscription before an action is available to the user. [TBD - Should we talk about the 1st party UI experience of inactive. I.e. Is it shown, but greyed out? Also, for later, are there hosting app requirements around indicating availability?]. Call [ActionRuntime.GetActionAvailability- TBD link needed]() to retrieve the current availability status of an action.

[TBD: The following snippet is currently psuedocode because the runtime initialization API is not available yet]

```csharp
void SetActionAvailability(bool actionIsAvailable)
{

    var runtime = //TBD create runtime

    using (runtime)
    {
        runtime.SetActionAvailability("ExampleActionProvider.WindowsActionHandler.SendMessage", actionIsAvailable);
    }

}
```




### Referencing remote files 

### Related content

* [Add Streaming text output to a Windows App Action](action-streaming-text.md)
* [Related article title](link.md)
* [Related article title](link.md)