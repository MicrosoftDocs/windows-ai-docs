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



## Process remote files using Microsoft.AI.Actions code generation

```csharp
[ActionProvider]
public sealed class MyActionsProvider
{
    [WindowsAction(Description = "Summarize a remote file", Icon = "ms-resource://Files/Assets/LockScreenLogo.png", UsesGenerativeAI = false)]
    //[WindowsActionInputCombination(Inputs = ["RemoteFileToSummarize"], Description = "Summarize ${RemoteFileToSummarize.SourceUri}"), Where = "${RemoteFileToSummarize.SourceId} == 'Contoso.Cloud' || ${RemoteFileToSummarize.SourceId} == 'Fabrikam.Cloud'" ]

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

It is a good practice to use **where** clauses in the **inputCombinations** declaration to specify which remote file services the action supports. This keeps the action from being invoked for remote files associated with unsupported services.

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
