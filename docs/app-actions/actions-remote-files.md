---
title: Handle remote files with App Actions on Windows
description: Learn how to handle remote files with App Actions on Windows.
author: drewbatgit
ms.author: drewbat
ms.topic: how-to
ms.date: 04/30/2025

#customer intent: As a developer, I want implement an App Action for Windows so that I can provide a unit of functionality that can be accessed from the App Actions on Windows ecosystem.

---

# Handle remote files with App Actions on Windows

This article describes how to implement support for remote files that are hosted by a cloud service provider from an App Actions on Windows provider app. The **RemoteFile** entity type, represented in C# by the [RemoteFileActionEntity](/uwp/api/windows.ai.actions.remotefileactionentity) class, provides properties that are used by many cloud file storage providers, such as unique IDs for files and drives. The examples in this page show how to access the cloud file properties but do not actually retrieve or process the remote files, as these processes will vay between different apps and providers. For more information on the different entity types that can be used as inputs and outputs for actions, see [Action definition JSON schema for App Actions on Windows](actions-json.md).

## Process remote files using Microsoft.AI.Actions code generation

The following example shows how to implement remote file support in an action provider that uses the [Microsoft.AI.Actions NuGet Package](https://www.nuget.org/packages/Microsoft.AI.Actions) which automatically generates the **IActionProvider** implementation based on .NET attributes in your code, allowing you to make strongly typed classes to represent your actions. For information about creating an action provider app using the **Microsoft.AI.Actions NuGet package, see [Get started with App Actions on Windows](actions-get-started.md).

Note that a **Where** clause is used in the [WindowsActionInputCombination](/windows/actions-source-generation/api/microsoft.ai.actions.annotations.windowsactioninputcombinationattribute) attribute to specify the cloud providers that are supported by this action. It's always a good idea to use where clauses like this to prevent your action from being presented to users for scenarios that your action doesn't support.

```csharp
[ActionProvider]
public sealed class MyActionsProvider
{
    [WindowsAction(Description = "Summarize a remote file", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]

    [WindowsActionInputCombination(Description = "Summarize ${RemoteFileToSummarize}", 
        Inputs = ["RemoteFileToSummarize"], 
        Where = ["${RemoteFileToSummarize.SourceId} == 'Contoso.Cloud' || ${RemoteFileToSummarize.SourceId} == 'Fabrikam.Cloud'"])]
    public async Task<SummarizeRemoteFileResult> SummarizeRemoteFile([Entity(Name = "RemoteFileToSummarize")] RemoteFileActionEntity remoteFile, InvocationContext context)
    {
        var summary = "";
        var remoteFileContents = "";

        switch (remoteFile.SourceId)
        {
            case "Contoso.Cloud":
                // Retrieve the file contents using the properties used by the "Contoso" service 
                remoteFileContents = GetContosoCloudFile(remoteFile.AccountId, remoteFile.DriveId, remoteFile.FileId);
                summary = SummarizeFile(remoteFileContents);
                break;

            case "Fabrikam.Cloud":
                // Retrieve the file contents using the properties used by the "Fabrikam" service 
                remoteFileContents = GetFabrikamCloudFile(remoteFile.AccountId, remoteFile.SourceUri);
                summary = SummarizeFile(remoteFileContents);
                break;

        }

        
        var entityFactory = context.EntityFactory;

        return new SummarizeRemoteFileResult
        {
            Text = entityFactory.CreateTextEntity(summary)
        };
    }

    public record SummarizeRemoteFileResult
    {
        public required TextActionEntity Text { get; init; }
    }
```


## JSON action definition with remote file input

The code example below shows an action definition JSON file that declares an action that uses the **RemoteFile** entity. If you are using the **Microsoft.AI.Actions** NuGet to auto-generate your JSON action definition file, this is what the file will look like after you have implemented the action as shown in the previous section.

```json
{
  "version": 3,
  "actions": [
    {
      "id": "ExampleAppActionProvider.MyActionsProvider.SummarizeRemoteFile",
      "description": "Summarize a remote file",
      "icon": "ms-resource://Files/Assets/LockScreenLogo.png",
      "usesGenerativeAI": false,
      "allowedAppInvokers" :  ["*"],
      "inputs": [
        {
          "name": "RemoteFileToSummarize",
          "kind": "RemoteFile"
        }
      ],
      "inputCombinations": [
        {
          "inputs": [
            "RemoteFileToSummarize"
          ],
          "where": [
            "${RemoteFileToSummarize.SourceId} == 'Contoso.Cloud' || ${RemoteFileToSummarize.SourceId} == 'Fabrikam.Cloud'"
          ],
          "description": "Summarize ${RemoteFileToSummarize}"
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
        "clsid": "00001111-aaaa-2222-bbbb-3333cccc4444"
      }
    }
  ]
}
```


## Process remote files with IActionProvider

This section shows how to implement an action that accepts a **RemoteFile** entity as an input when you are manually implementing the **IActionProvider** interface. You will also need to manually add an action definition JSON file like the one shown in the previous section to your project. For more information on implementing a COM-based action provider without using the **Microsoft.AI.Actions** gode generation feature, see [Manually implement IActionProvider](actions-iactionprovider-manual.md). 

```csharp
public IAsyncAction InvokeAsync(ActionInvocationContext context)
{
    return InvokeAsyncHelper(context).AsAsyncAction();
}

async Task InvokeAsyncHelper(ActionInvocationContext context)
{
    NamedActionEntity[] inputs = context.GetInputEntities();

    var actionId = context.ActionId;
    switch (actionId)
    {
        case "ExampleAppActionProvider.MyActionsProvider.SummarizeRemoteFile":
            var summary = "";
            var remoteFileContents = "";

            foreach (NamedActionEntity inputEntity in inputs)
            {
                if (inputEntity.Name.Equals("RemoteFileToSummarize", StringComparison.Ordinal))
                {
                    var remoteFile = (RemoteFileActionEntity)inputEntity.Entity;
                    switch (remoteFile.SourceId)
                    {
                        case "Contoso.Cloud":
                            // Retrieve the file contents using the properties used by the "Contoso" service 
                            remoteFileContents = GetContosoCloudFile(remoteFile.AccountId, remoteFile.DriveId, remoteFile.FileId);
                            summary = SummarizeFile(remoteFileContents);
                            break;

                        case "Fabrikam.Cloud":
                            // Retrieve the file contents using the properties used by the "Fabrikam" service 
                            remoteFileContents = GetFabrikamCloudFile(remoteFile.AccountId, remoteFile.SourceUri);
                            summary = SummarizeFile(remoteFileContents);
                            break;

                    }
                }
            }
            
            TextActionEntity result = context.EntityFactory.CreateTextEntity(summary);
            context.SetOutputEntity("Text", result);


            break;

        default:
            break;

    }
}
```
