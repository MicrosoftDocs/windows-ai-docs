---
title: Detect and filter callers for App Actions on Windows
description: Learn about different mechanisms for restricting which apps can query or invoke App Actions on Windows.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---

# Detect and filter callers for App Actions on Windows

This article describes several mechanisms that provider apps for App Actions on Windows can implement to limit the set of apps that can query for or invoke an action. This is an optional implementation step that is designed to support scenarios such as actions that consume resources that have per-use costs, such as subscription-based LLM services.

## Set allowedAppInvokers in the action definition JSON file

The action definition JSON file, which is used to register an action with the system, includes an *allowedAppInvokers* field for each action definition. The value of this field is a list of Application User Model IDs (AUMID) that can discover the action through a call to [GetActionsForInputs](/uwp/api/windows.ai.actions.hosting.actioncatalog.getactionsforinputs) or [GetAllActions](/uwp/api/windows.ai.actions.hosting.actioncatalog.getallactions). Wildcards are supported. "\*" will match all AppUserModelIDs. If **allowedAppInvokers** is omitted or is an empty list, no apps will be able to discover the action. For more information on AppUserModelIDs, see [Application User Model IDs](/windows/win32/shell/appids). For details of the action definition JSON file, see [Action definition JSON schema for App Actions on Windows](actions-json.md).

Note that the *allowedAppInvokers* field only limits which apps can query for an action. Because of the ways in which action providers are registered with the system, it's still possible for apps to invoke an action, even if they are restricted from querying by *allowedAppInvokers*. The rest of this article discusses different ways of restricting the callers for an action at runtime.

## Detect the caller when using URI launch activation

The following section describes different ways of detecting the caller of an action at runtime for action provider apps that use URI launch activation.

### Get caller Package Family Name (Rich activation only)

Provider apps that use modern, rich activation can check the value of the [CallerPackageFamilyName](/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs.callerpackagefamilyname) property of the [ProtocolForResultsActivatedEventArgs](/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs) class passed into your provider app on launch. If the value ends with the string "_cw5n1h2txyewy", then the action was invoked by Windows. For more information on rich activation, see [Rich activation with the app lifecycle API](/windows/apps/windows-app-sdk/applifecycle/applifecycle-rich-activation).

```csharp
const string windowsPFN = "_cw5n1h2txyewy";
```

```csharp
protected override void OnLaunched(Microsoft.UI.Xaml.LaunchActivatedEventArgs args)
{
    var eventargs = AppInstance.GetCurrent().GetActivatedEventArgs();

    if ((eventargs != null) && (eventargs.Kind == ExtendedActivationKind.ProtocolForResults))
    {

        ProtocolForResultsActivatedEventArgs? protocolForResultsArgs = eventargs.Data as ProtocolForResultsActivatedEventArgs;

        if (protocolForResultsArgs.CallerPackageFamilyName.EndsWith(windowsPFN))
        {
            // Perform the action if invoked by Windows
        }
        
    }

    _window = new MainWindow();
    _window.Activate();
}
```

### Use the $.Token URI query string parameter

Action providers that use URI activation declare the URI that launches their action in the action definition JSON manifest file. There is a special query string parameter `$.Token` that instructs the Action Runtime to pass a unique identifier token on the query string of the launch URI when your app is invoked. The following example shows the syntax for declaring a URI with the `$.Token` parameter.

```json
"invocation": {
  "type": "Uri",
  "uri": "urilaunchaction-protocol://messageaction?token=${$.Token}",
  "inputData": {
    "message": "${message.Text}"
  }
}
```

When your action is launched, retrieve the query string parameter `Token` from the activation URI. Pass this token into a call to [GetActionInvocationContextFromToken](/uwp/api/windows.ai.actions.actionruntime.getactioninvocationcontextfromtoken?). The Action Runtime will validate the token against the token it provided when launching the action provider. If the values match, a valid [ActionInvocationContext](/uwp/api/windows.ai.actions.actioninvocationcontext) object is returned, validating that the action was invoked by the Action Runtime.

```csharp
protected override void OnLaunched(Microsoft.UI.Xaml.LaunchActivatedEventArgs args)
{
    var eventargs = AppInstance.GetCurrent().GetActivatedEventArgs();

    if ((eventargs != null) && (eventargs.Kind == ExtendedActivationKind.ProtocolForResults))
    {

        ProtocolForResultsActivatedEventArgs? protocolForResultsArgs = eventargs.Data as ProtocolForResultsActivatedEventArgs;

        using (ActionRuntime runtime = ActionRuntimeFactory.CreateActionRuntime())
        {

            var launchUri = protocolForResultsArgs.Uri;
            var query = HttpUtility.ParseQueryString(launchUri.Query);
            var token = query["Token"];
            if(token != null)
            {
                var context = runtime.GetActionInvocationContextFromToken(token);
                if(context == null)
                {
                    // Caller is not Action Runtime
                    return;
                }
            }
```

 Note that this example uses helper methods provided by the Microsoft.AI.Actions NuGet package. For information on referencing this package from an action provider, see [Implement URI launch for App Actions on Windows](actions-uri-launch.md).

## Detect the caller at runtime from COM activation

The following section walks through the process of retrieving the AUMID of the app that invoked an action at runtime. The examples in this section will check for the AUMID of the Action Runtime. Apps could choose to filter on the AUMID of other apps or use the Package Family Name (PFN) to filter on all apps that are contained in a particular package.

### Add a reference to the CsWin32 Nuget package

The example code shown in this section uses Win32 APIs to get the package family name of the caller. In order to more easily call Win32 APIs from a C# app, this example will use the CsWin32 NuGet package, which generates C# wrappers for Win32 APIs at build time. For more information, see the [CsWin32 github repo](https://github.com/microsoft/CsWin32).

1. In **Solution Explorer**, right-click on your project and select **Manage NuGet Packages...**.
1. On the **Browse** tab, search for "Microsoft.Windows.CsWin32".
1. Select this package and click **Install**, following all of the prompts.

When you install this package, it will show you a readme detailing how to specify the Win32 APIs for which C# wrappers will be generated. To do so, you will need to create a text file that lists the methods you will be using.

1. In **Solution Explorer**, right-click on your project and select **Add->New Item...**.
1. Select the **TextFile** template.
1. Name the file "NativeMethods.txt" and click **Add**
1. Double-click NativeMethods.txt to open the file and add the following lines.

```text
CoRegisterClassObject
CoRevokeClassObject
CoImpersonateClient
OpenThreadToken
GetCurrentThread
CoRevertToSelf
GetPackageFamilyNameFromToken
GetLastError
```

### Update your ActionProvider class to check the action invoker PFN

The following helper method uses the Win32 APIs specified in NativeMethods.txt to retrieve the AUMID for the calling process and returns true if it matches the AUMID that is passed in. For detailed explanation about how this code works, see [Impersonating a Client](/windows/win32/wmisdk/impersonating-a-client).

```csharp
static bool WasInvokedByAUMID(string aumid)
{
    Windows.Win32.Foundation.HRESULT hr = PInvoke.CoImpersonateClient();

    var RPC_E_CALL_COMPLETE = unchecked((int)0x80010117);

    if (hr.Value == RPC_E_CALL_COMPLETE)
    {
        return false;
    }

    Microsoft.Win32.SafeHandles.SafeFileHandle hThreadTok;


    bool bRes = PInvoke.OpenThreadToken(
        new Microsoft.Win32.SafeHandles.SafeFileHandle(PInvoke.GetCurrentThread(), true),
        Windows.Win32.Security.TOKEN_ACCESS_MASK.TOKEN_QUERY,
        true,
        out hThreadTok
    );

    if (bRes == false)
    {
        var e = Marshal.GetLastWin32Error();
        Console.WriteLine("Unable to read thread token. " + e);
        hThreadTok.Close();
        return false;
    }


    PInvoke.CoRevertToSelf();


    var invokerAumid = new Span<char>();
    uint aumidLength = 0;

    PInvoke.GetApplicationUserModelIdFromToken(hThreadTok, ref aumidLength, invokerAumid);
    invokerAumid = new char[aumidLength].AsSpan();
    PInvoke.GetApplicationUserModelIdFromToken(hThreadTok, ref aumidLength, invokerAumid);

    hThreadTok.Close();

    if (invokerAumid.Trim('\0').EndsWith(aumid))
    {
        return true;
    }
    else
    {
        return false;
    }
}
```

Because of the way that the [CoImpersonateClient](/windows/win32/api/combaseapi/nf-combaseapi-coimpersonateclient) is implemented, it's important that you call the helper method from within the invocation of your action and not from within your app's launch procedure.

The following example illustrates a check for the Action Runtime AUMID in an action implemented using the Microsoft.AI.Actions code generation framework.

```csharp
const string actionRuntimeAUMID = "_cw5n1h2txyewy!ActionRuntime";
```

```csharp
[WindowsActionInputCombination(
    Inputs = ["Contact", "Message"],
    Description = "Send '${Message.Text}' to '${Contact.Text}'"
)]
public async Task<SendMessageResult> SendMessage(
    [Entity(Name = "Contact")] string contact,
    [Entity(Name = "Message")] string? message,
    InvocationContext context)
{
    if (!WasInvokedByAUMID(actionRuntimeAUMID))
    {
        context.Result = ActionInvocationResult.Unavailable;
        return new SendMessageResult
        {
            Text = context.EntityFactory.CreateTextEntity("")
        };
    }
    
    // Your action logic here
    string result = await ProcessMessageAsync(contact, message);

    return new SendMessageResult
    {
        Text = context.EntityFactory.CreateTextEntity(result)
    };
}
```