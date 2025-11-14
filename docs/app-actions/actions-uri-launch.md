---
title: Implement URI launch for App Actions on Windows
description: Learn how to handle URI launch activation for App Actions on Windows.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---
# Implement URI launch for App Actions on Windows

This article describes the steps for creating app actions and describes the components of an App Action provider app. App actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. For more information about App Actions on Windows, see [App Actions on Windows Overview](index.md).

Action provider apps can be implemented to use COM activation or URI launch activation. URI launch actions don't support some advanced action features such as displaying UI in context or streaming text results, but the implementation is very simple and may be the best choice for actions that only consist of a single request and response.

Provider apps that use COM activation implement the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider) interface to handle action invocation. This method enables advanced action features such as support for streaming text, but requires more code than using the Windows App Actions VSIX Extension. For information about using COM activation in an app provider, see [Get started with App Actions on Windows](actions-get-started.md).

## [Automated dependency installation (recommended)](#tab/winget)

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

## [Manual dependency installation](#tab/manual)

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

The App Actions feature is supported for multiple app frameworks and languages, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "WinUI Blank App (Packaged)" project template.
1. Name the new project "ExampleAppActionProvider".
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". For **Target OS version** and **Supported OS version**, select version 10.0.26100.0 or greater.
1. To update the project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.75</WindowsSdkPackageVersion>
    ```

## Add a reference to the Microsoft.AI.Actions nuget package

The Microsoft.AI.Actions nuget package enables you to initialize the app actions runtime, which provides APIs for creating the entity objects that are passed as inputs to and outputs from app actions.

1. In **Solution Explorer**, right-click the project name and select **Manage NuGet Packages...**
1. Make sure you are on the **Browse** tab and search for Microsoft.AI.Actions.
1. Select Microsoft.AI.Actions and click Install.


## Add an action definition JSON file

Action provider apps must provide an action definition file that defines the actions the app implements. This file provides information such as the unique identifier and description for each action as well as the names and types of inputs and outputs each action supports. For more information about the App Action JSON file format, see [Action definition JSON schema for Windows App Action providers](actions-json.md).

This example will define one action called **SendMessage**, that takes a single **Text** entity as input, and returns a single **TextEntity** as output. In addition to defining actions, the JSON file also specifies whether the action provider app should be launched using COM activation or via URI launch. This example will use URI activation. The URI scheme `urilaunchaction-protocol` will be registered in a later step in this walkthrough.

1. In **Solution Explorer**, right-click the `Assets` folder and select **Add->New Item...**. 
1. In the **Add New Item** dialog, select **Text File**. Name the new file "registration.json", and click OK.
1. In **Solution Explorer**, right-click the registration.json file and select **Properties**. In the **Properties** pane, set **Build Action** to "Content" and set **Copy to Output Directory** to "Copy if Newer".
1. Add the following JSON action definition to the registration.json file.





```json
{
  "version": 3,
  "actions": [
    {
      "id": "ExampleActionProvider.SendMessage",
      "description": "Send a message (URI Launch)",
      "icon": "ms-resource://Files/Assets/LockScreenLogo.png",
      "usesGenerativeAI": false,
      "allowedAppInvokers": ["*"],
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
        "type": "Uri",
        "uri": "urilaunchaction-protocol://",
        "inputData": {
          "message": "${message.Text}"
        }
      }
    }
  ]
}
```

## Update the app package manifest file

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, the app must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines the app's actions. For more information on the action provider app package manifest format, see [App Actions on Windows package manifest XML format](actions-provider-manifest.md).

In order for an app action provider to be launched via URI, it must register a protocol with the system. This registration is made by providing the [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The *Name* attribute of the **Protocol** element must match the **invocation.uri** value specified in the Action definition JSON file, which for this example is `urilaunchaction-protocol`. For more information on URI launch activation, see [Launch an app for results](/windows/uwp/launch-resume/how-to-launch-an-app-for-results).

1. Right-click the Package.appxmanifest file and select **View Code**
2. Add the following namespace to the **Package** element at the root of the file.

```xml
xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
```

3. Add the following **Extensions** element inside the **Application** element and after the **VisualElements** element. 

```xml
<Extensions>
  <uap:Extension Category="windows.protocol">
    <uap:Protocol Name="urilaunchaction-protocol" ReturnResults="always">
      <!-- app-defined protocol name -->
      <uap:DisplayName>URI Launch Action Scheme</uap:DisplayName>
    </uap:Protocol>
  </uap:Extension>
  <uap3:Extension Category="windows.appExtension">
    <uap3:AppExtension Name="com.microsoft.windows.ai.actions" DisplayName="URILaunchAction" Id="UriLaunchActionId" PublicFolder="Assets">
      <uap3:Properties>
        <Registration xmlns="">registration.json</Registration>
      </uap3:Properties>
    </uap3:AppExtension>
  </uap3:Extension>
</Extensions>
```

## Handle app activation

When the app action provider is launched from its registered URI schema, the inputs for the action can be accessed through the [AppActivationArguments](/windows/windows-app-sdk/api/winrt/microsoft.windows.applifecycle.appactivationarguments) object that is obtained by calling the [AppInstance.GetActivatedEventArgs](/windows/windows-app-sdk/api/winrt/microsoft.windows.applifecycle.appinstance.getactivatedeventargs). To make sure that the activation was from the registered protocol, you must first check to make sure that the value of the [Kind](/windows/windows-app-sdk/api/winrt/microsoft.windows.applifecycle.appactivationarguments.kind) property is [ExtendedActivationKind.ProtocolForResults](/windows/windows-app-sdk/api/winrt/microsoft.windows.applifecycle.extendedactivationkind). If so, you can cast the arguments object to a [ProtocolForResultsActivatedEventArgs](/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs) object.

> [!NOTE]
> This example uses the **ProtocolForResultsActivatedEventArgs** object to verify that the action was invoked by Windows, and exits without completing if the action was launched by another caller. For more information see, [Detect and filter callers for App Actions on Windows](actions-filter-caller.md).

The inputs for the action are accessed through the [Data](/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs.data) property of the event args, which is a [ValueSet](/uwp/api/windows.foundation.collections.valueset) that contains key/value pairs for each input entity. This example gets the `message` input entity as defined in the registration.json file. The value of this input is a text string.

In order to return results for the action, the app action provider must instantiate one or more output entities. This example returns a single **TextActionEntity** containing the response to the input message.

To instantiate a new output entity, get an instance of the [ActionEntityFactory](/uwp/api/windows.ai.actions.actionentityfactory) class from the [EntityFactory](/uwp/api/windows.ai.actions.actionruntime.entityfactory) property of the [ActionRuntime](/uwp/api/windows.ai.actions.actionruntime) object. The factory object provides methods for instantiating all of the different types of action entities. After creating the text entity containing our response message, we make an entry in a new **ValueSet** where the key is the output name specified in the registrationl.json file, "response", and the value is the [Id](/uwp/api/windows.ai.actions.actionentity.id) of the **TextActionEntity**. Note that when you create the entity by calling [CreateTextEntity](/uwp/api/windows.ai.actions.actionentityfactory.createtextentity), the entity is registered with the action runtime. This is why you add the **Id** of the entity as the value in the **ValueSet**. Consumers of your action will retrieve the entity object from the action runtime using this ID.

The final step in handling URI activation is to create a new [ProtocolForResultsOperation](/uwp/api/windows.system.protocolforresultsoperation) and call the [ReportCompleted](/uwp/api/windows.system.protocolforresultsoperation.reportcompleted) method, passing in the **ValueSet** that contains the output entity reference.

In App.xaml.cs, replace the default implementation of **OnLaunched** with the following code.

```csharp
  ProtocolForResultsOperation? _operation;
  protected override void OnLaunched(Microsoft.UI.Xaml.LaunchActivatedEventArgs args)
  {
      var eventargs = Microsoft.Windows.AppLifecycle.AppInstance.GetCurrent().GetActivatedEventArgs();

      if ((eventargs != null) && (eventargs.Kind == ExtendedActivationKind.ProtocolForResults))
      {
          ProtocolForResultsActivatedEventArgs? protocolForResultsArgs = eventargs.Data as ProtocolForResultsActivatedEventArgs;
      
          if (protocolForResultsArgs.CallerPackageFamilyName.EndsWith("_cw5n1h2txyewy"))
          {
              using (ActionRuntime runtime = ActionRuntimeFactory.CreateActionRuntime())
              {
                  if (protocolForResultsArgs != null)
                  {
                      ValueSet inputData = protocolForResultsArgs.Data;
                      var message = inputData["message"];
      
                      Windows.AI.Actions.ActionEntityFactory source = runtime.EntityFactory;
                      Windows.AI.Actions.ActionEntity textEntity = source.CreateTextEntity("Message response.");
      
                      ValueSet result = new ValueSet();
                      result["response"] = textEntity.Id;
      
                      _operation = protocolForResultsArgs.ProtocolForResultsOperation;
                      _operation.ReportCompleted(result);
                  }
              }
          }
      }

      _window = new MainWindow();
      _window.Activate();
  }

```



## Test your Windows App Action

The App Actions Testing Playground app allows you to validate the registration and functionality of your Windows App Action provider app. For more information on using this tool, see [App Actions Testing Playground app](actions-test-tool.md).

## Additional App Actions on Windows features

The following articles provide information about additional features of App Actions on Windows.

- [Toggle availability of an App Action for Windows](actions-availability.md)


### Related content

