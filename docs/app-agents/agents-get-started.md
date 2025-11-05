---
title: Get started with App Agents on Windows
description: Learn how to create a provider app for App Agents on Windows.
ms.topic: how-to
ms.date: 11/05/2025

#customer intent: As a developer, I want implement an App Agent for Windows so that I can provide implement and register AI-based agents that can be invoked to perform tasks autonomously.

---
# Get started with App Agents on Windows

This article describes the steps for creating app agents and describes the components of an App Agent provider app. App agents are software  that perceives its environment, reasons, and takes actions toward achieving specific goals with some degree of autonomy. App Agents on Windows allows a Windows app to implement and register agents so that they can be accessed from other apps and experiences. For more information, see [App Agents on Windows Overview](index.md). 

## Implement an action provider

App agents are a specialized implementation of an App Actions on Windows provider app. For this tutorial, you should begin by implementing an action provider by following the guidance in [Get started with App Actions on Windows](../app-actions/actions-get-started.md). The rest of this article will show the additions and modifications you will make to the app action in order for it to be called as an app agent.

## Modify the action provider to support the required input and output entities

For an app action to be invoked by the system as an agent, it must meet the following requirements for input and output entities:

* One input must be a [TextActionEntity](/uwp/api/windows.ai.actions.textactionentity) with the name `agentName`.  

* One input must be a **TextActionEntity** with the name `prompt`.  

* All input combinations for the action must accept both the `agentName` and `prompt` entities.

TBD - update the action provider code below to use the required entities  

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

The following example shows the action definition file generated from the action provider class definition shown in the previous example.

TBD - Update the action description below from the spec to align with the action provider sample code above

```json
{ 
  "version": 3,  
  "actions": [  
    {  
      "id": "ContosoAgentAction",  
      "description": "Start an agent for Contoso",  
      "icon": "ms-resource://Files/Assets/ContosoLogo.png",  
      "usesGenerativeAI": true,  
      "allowedAppInvokers": ["*"],  
      "inputs": [  
        {  
          "name": "agentName", "kind": "Text"  
        },  
        {  
          "name": "prompt", "kind": "Text"  
        },  
        {  
          "name": "attachedFile", "kind": "File"  
        }  
      ],  
      "inputCombinations": [  
        // If we don't always expect attachedFile to be present, we can 
        // declare two different inputCombinations  
        {  
          "inputs": [ "agentName", "prompt" ],  
          "description": "Start Contoso Agent with '${agentName.Text}'."  
        },  
        {  
          "inputs": [ "agentName", "prompt", "attachedFile" ],  
          "description": "Start Contoso Agent with '${agentName.Text}' and additional context."  
        }  
      ],  
      "outputs": [],  
      "invocation": {  
        "type": "Uri",  
        // Example of a valid launch URI using the inputs defined above   
        "uri": "contosoLaunch:invokeAgent?agentName=${agentName.Text}&prompt=${prompt.Text}&context=${attachedFile.Path}"  
      }  
    }  
  ]  
} 
```

## Create an agent definition JSON file

[Steps to add a file to the project.]

The value of the **action_id** field in the agent definition manifest must match the **id** field specified in the action definition manifest for an action included in the same app package.

TBD - Update the agent definition file to match the rest of the tutorial

```json
{ 
  "manifest_version": "0.1.0", 
  "version": "1.0.0", 
  "name": "Contoso.TripPlannerAgent", 
  "display_name": "ms-resource://tripPlannerAgentDisplayName", 
  "description": "ms-resource://tripPlannerAgentDescription", 
  "icon": "ms-resource://tripPlannerAgentIcon.png", 
  "action_id": "TripPlannerAction" // app action defined in same package 
} 
```


## Update the app package manifest file

The Package.appmanifest file provides the details of the MSIX package for an app. If you followed the get started tutorial for app actions, then you have already added a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element to register the action by setting the extension **Name** attribute to `com.microsoft.windows.ai.actions`. To register the action as an agent, you will need to add another app extension with the **Name** set to `com.microsoft.windows.ai.agent`. Under the **Properties** element of the app extension element, you must provide a **Registration** element that specifies the location of your agent definition JSON file.

```xml
<uap3:Extension Category="windows.appExtension"> 

    <uap3:AppExtension 
      Name="com.microsoft.windows.ai.agent" 
      Id="TripPlannerAction" 
      DisplayName="Contoso Trip Planner" 
      PublicFolder="Assets"> 
        <uap3:Properties> 
          <Registration>manifest.json</Registration> 
        </uap3:Properties> 
    </uap3:AppExtension> 
  </uap3:Extension> 
```


## Test the Windows App Agent

TBD