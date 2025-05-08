---
title: Get started with App Actions on Windows
description: Learn how to create an App Actions on Windows provider app and understand the components of an action provider app.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/23/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---


# Get started with App Actions on Windows

This article describes the steps for creating a App Actions on Windows provider app and describes the components of an App Action provider app. App actions are individual units of behavior that a Windows app can implement and register so that they can be accessed from other apps and experiences, seamlessly integrating into user workflows. For more information about App Actions on Windows, see [App Actions on Windows Overview](index.md)


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

The App Actions on Windows feature is supported for multiple app frameworks, but apps must have package identity to be able to register with the system. This walkthrough will implement a Windows App Action provider in a packaged C# WinUI 3 desktop app.

1. In Visual Studio, create a new project. 
1. In the **Create a new project** dialog, set the language filter to "C#" and the platform filter to "WinUI", then select the "Blank App, Packaged (WinUI 3 in Desktop)" project template.
1. Name the new project "ExampleAppActionProvider".
1. When the project loads, in **Solution Explorer** right-click the project name and select **Properties**. On the **General** page, scroll down to **Target OS** and select "Windows". For **Target OS version** and **Supported OS version**, select version 10.0.26100.0 or greater.
1. To update your project to support the Action Provider APIs, in **Solution Explorer** right-click the project name and select **Edit Project File**. Inside of **PropertyGroup**, add the following **WindowsSdkPackageVersion** element.

    ```xml
    <WindowsSdkPackageVersion>10.0.26100.59-preview</WindowsSdkPackageVersion>
    ```

## Add an action definition JSON file

Action provider apps must provide an action definition file that defines the actions the app implements. This file provides information about the inputs and outputs of your actions and metadata such as a unique identifier and a description for your actions. For more information about the App Action JSON file format, see [Action definition JSON schema for Windows App Action providers](action-json.md).

This example will define one action called **SendMessage**, that takes a single **Text** entity as input, and returns a single **TextEntity** as output. In addition to definiing actions, the JSON file also specifies whether the action provider app should be launched using COM activation or via URI launch. This example will use COM activation.

1. In **Solution Explorer**, right-click the ExampleAppActionProvider project file and select **Add->New Item...**. 
1. In the **Add New Item** dialog, select **Text File**. Name the new file "registration.json", and click OK.
1. Add the following JSON action definition to the registration.json file.
1. In **Solution Explorer**, right-click the registration.json file and select **Properties**. In the **Properties** pane, set **Build Action** to "Content" and set **Copy to Output Directory** to "Copy if Newer".
1. Replace the **invocation.clsid** value with a new GUID that will identify the provider. You can generate a new GUID in Visual Studio by going to **Tools->Create GUID**. This GUID will be used again in a few different places in this walkthrough.



```json
{
  "version": 2,
  "actions": [
    {
      "id": "ExampleAppActionProvider.SendMessage",
      "description": "Send a message",
      "icon": "ms-resource://Files/Assets/StoreLogo.png",
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

Action providers must implement the [IActionProvider](/uwp/api/windows.ai.actions.provider.iactionprovider). This interface requires the implementation of a single method, [InvokeAsync](/uwp/api/windows.ai.actions.provider.iactionprovider.invokeasync), which the system uses to invoke an action.

1. In Visual Studio, right-click the `AppActionProvider` project in **Solution Explorer** and select **Add->Class**. 
2. In the **Add class** dialog, name the class "ActionProvider" and click **Add**. 
3. In the generated ActionProvider.cs file, update the class definition to indicate that it implements the **IActionProvider** interface.
4. Label the class with the [System.Runtime.InteropServices.GuidAttribute](/dotnet/api/system.runtime.interopservices.guidattribute). This is used by the COM activation code shown later in this walkthrough. Be sure to update the value to the value specified in the **invocation.clsid** field in the registration.json file.


```csharp
// AppActionProvider.cs
[System.Runtime.InteropServices.GuidAttribute("00001111-aaaa-2222-bbbb-3333cccc4444")] 
public partial class AppActionProvider : IActionProvider
```

## Implement IActionProvider.InvokeAsync

The **InvokeAsync** method has a return type of [IAsyncAction](/uwp/api/windows.foundation.iasyncaction). This example uses a helper class that returns a [Task](/dotnet/api/system.threading.tasks.task), which is then converted to an **IAsyncAction** with a call to **AsAsyncAction** extension method. Add the following method definition to the **AppActionProvider** class.

```csharp
// AppActionProvider.cs
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

Add the following method definition to the **AppActionProvider** class.

```csharp
// AppActionProvider.cs
async Task InvokeAsyncHelper(ActionInvocationContext context)
{
    NamedActionEntity[] inputs = context.GetInputEntities();

    var actionId = context.ActionId;
    switch (actionId)
    {
      case "ExampleActionProvider.SendMessage":
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

The Package.appmanifest file provides the details of the MSIX package for an app. To be registered by the system as a Windows App Action provider, you must include a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element with the **Category** set to "windows.appExtension". This element is used to specify the location of the App Action JSON file that defines your app's actions. For more information on the action provider app package manifest format, see [Windows App Action provider package manifest XML format](action-provider-manifest.md).

The example in this walkthrough uses COM activation to launch the app action  provider. To enable COM activation, use the [com2:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-com2-extension) element in the app package manifest. The **invocation.clsid** value specified in the Action definition JSON file must match the class ID specified in the [com:Class](/uwp/schemas/appxpackage/uapmanifestschema/element-com-exeserver-class) element in the app package manifest.

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
            <com:Class Id="00001111-aaaa-2222-bbbb-3333cccc4444" DisplayName="ExampleAppActionProvider" />
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

## Implement a class factory that will instantiate IActionProvider on request

After the system launches the action provider app, the app must call [CoRegisterClassObject](/windows/win32/api/combaseapi/nf-combaseapi-coregisterclassobject) in so that the system can instantiate the COM server for the **IActionProvider** implementation. This function requires an implementation of the [IClassFactory](/windows/win32/api/unknwn/nn-unknwn-iclassfactory).  This example implements the class factory in a self-contained helper class. 

In Visual Studio, right-click the `ExampleAppActionProvider` project in **Solution Explorer** and select **Add->Class**. In the **Add class** dialog, name the class "FactoryHelper" and click **Add**.

Replace the contents of the FactoryHelper.cs file with the following code. This code defines the **IClassFactory** interface and implements it's two methods, [CreateInstance](/windows/win32/api/unknwn/nf-unknwn-iclassfactory-createinstance) and [LockServer](/windows/win32/api/unknwn/nf-unknwn-iclassfactory-lockserver). This code is typical boilerplate for implementing a class factory and is not specific to the functionality of a widget provider except that we indicate that the class object being created implements the **IActionProvider** interface. 

```csharp
// FactoryHelper.cs

using Microsoft.Windows.Widgets.Providers;
using System.Runtime.InteropServices;
using WinRT;

namespace COM
{
    static class Guids
    {
        public const string IClassFactory = "00000001-0000-0000-C000-000000000046";
        public const string IUnknown = "00000000-0000-0000-C000-000000000046";
    }

    /// 
    /// IClassFactory declaration
    /// 
    [ComImport, ComVisible(false), InterfaceType(ComInterfaceType.InterfaceIsIUnknown), Guid(COM.Guids.IClassFactory)]
    internal interface IClassFactory
    {
        [PreserveSig]
        int CreateInstance(IntPtr pUnkOuter, ref Guid riid, out IntPtr ppvObject);
        [PreserveSig]
        int LockServer(bool fLock);
    }

    [ComVisible(true)]
    class WidgetProviderFactory<T> : IClassFactory
    where T : IActionProvider, new()
    {
        public int CreateInstance(IntPtr pUnkOuter, ref Guid riid, out IntPtr ppvObject)
        {
            ppvObject = IntPtr.Zero;

            if (pUnkOuter != IntPtr.Zero)
            {
                Marshal.ThrowExceptionForHR(CLASS_E_NOAGGREGATION);
            }

            if (riid == typeof(T).GUID || riid == Guid.Parse(COM.Guids.IUnknown))
            {
                // Create the instance of the .NET object
                ppvObject = MarshalInspectable<IActionProvider>.FromManaged(new T());
            }
            else
            {
                // The object that ppvObject points to does not support the
                // interface identified by riid.
                Marshal.ThrowExceptionForHR(E_NOINTERFACE);
            }

            return 0;
        }

        int IClassFactory.LockServer(bool fLock)
        {
            return 0;
        }

        private const int CLASS_E_NOAGGREGATION = -2147221232;
        private const int E_NOINTERFACE = -2147467262;

    }
}

```

## Implement a custom Main method

In the default project template, the **Main** method entry point is autogenerated by the compiler. This example will disable the autogeneration of **Main** so that the necessary activation code can be run at startup.

1. In **Solution Explorer**, right-click the project icon and select **Edit Project File**.
1. In the **PropertyGroup** element, add the following child element to disable the auto-generated main function.

```xml
<DefineConstants>$(DefineConstants);DISABLE_XAML_GENERATED_MAIN</DefineConstants>
```

Next, in **Solution Explorer**, right-click the project icon and select **Add->Class**. Change the file name to "Program.cs" and click **Add**.


In the Program.cs file for our executable, we will call **CoRegisterClassObject** to register our action provider. Replace the contents of Program.cs with the following code. This code imports the **CoRegisterClassObject** function and calls it, passing in the **ActionProviderFactory** class defined in a previous step. Be sure to update the **CLSID_Factory** variable declaration to use the GUID you specified in the registration.json file.

```csharp
// Program.cs

using System.Runtime.InteropServices;
using ComTypes = System.Runtime.InteropServices.ComTypes;
using Microsoft.Windows.Widgets;
using ExampleWidgetProvider;
using COM;
using System;


[DllImport("ole32.dll")]

static extern int CoRegisterClassObject(
            [MarshalAs(UnmanagedType.LPStruct)] Guid rclsid,
            [MarshalAs(UnmanagedType.IUnknown)] object pUnk,
            uint dwClsContext,
            uint flags,
            out uint lpdwRegister);

[DllImport("ole32.dll")] static extern int CoRevokeClassObject(uint dwRegister);

);
uint cookie;

Guid CLSID_Factory = Guid.Parse("00001111-aaaa-2222-bbbb-3333cccc4444");
CoRegisterClassObject(CLSID_Factory, new ActionProviderFactory<AppActionProvider>(), 0x4, 0x1, out cookie);
]

Application.Start((p) =>
{
    var context = new DispatcherQueueSynchronizationContext(
        DispatcherQueue.GetForCurrentThread());
    SynchronizationContext.SetSynchronizationContext(context);
    _ = new App();
});
//}

PInvoke.CoRevokeClassObject(cookie);

return 0;
```


## Test your Windows App Action

The Windows Actions Test Tool allows you to validate the registration and functionality of your Windows App Action provider app. For more information on using this tool, see [Windows Actions Test Tool](actions-test-tool.md).

## Additional App Actions on Windows features

The following sections list some other scenarios that Windows App Action providers may choose to implement.

### Actions availability

A Windows App Action provider can inform the system of the availability of one or more of its registered actions by calling [ActionRuntime.SetActionAvailability](/uwp/api/windows.ai.actions.actionruntime.setactionavailability). This feature enables scenarios such as requiring a login or subscription before an action is available to the user. [TBD - Should we talk about the 1st party UI experience of inactive. I.e. Is it shown, but greyed out? Also, for later, are there hosting app requirements around indicating availability?]. Call [ActionRuntime.GetActionAvailability](/uwp/api/windows.ai.actions.actionruntime.getactionavailability) to retrieve the current availability status of an action.

[TBD: The following snippet is currently psuedocode because the runtime initialization API is not available yet]

```csharp
void SetActionAvailability(bool actionIsAvailable)
{

    var runtime = //TBD create runtime

    using (runtime)
    {
        runtime.SetActionAvailability("ExampleActionProvider.SendMessage", actionIsAvailable);
    }

}
```




### Referencing remote files 

### Related content

* [Add Streaming text output to a Windows App Action](action-streaming-text.md)
* [Related article title](link.md)
* [Related article title](link.md)