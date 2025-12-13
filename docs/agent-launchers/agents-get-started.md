---
title: Get started with Agent Launchers on Windows
description: Learn how to create a provider app for Agent Launchers on Windows.
ms.topic: how-to
ms.date: 11/05/2025

#customer intent: As a developer, I want to implement an Agent Launcher for Windows so that I can register or invoke AI-based agents that can be invoked to perform tasks autonomously.

---
# Get started with Agent Launchers on Windows

This article describes the steps for creating Agent Launchers and the components of an Agent Launcher provider app. Agent Launchers on Windows enable a Windows app to implement and register agents so that other apps and experiences can invoke them. For more information, see [Agent Launchers on Windows Overview](index.md).

## Implement an action provider

Agent Launchers rely on a specialized implementation of an App Actions on Windows provider app. For this tutorial, start by implementing an action provider by following the guidance in [Get started with App Actions on Windows](../app-actions/actions-get-started.md). The rest of this article shows the additions and modifications you need to make to the app action to register and call it as an Agent Launcher. It demonstrates how to register the agent both statically (registered at install time) and dynamically so that you can add and remove Agent Launchers per application logic.  

## Modify the action provider to support the required input entities

For an app action to be invoked as an Agent Launcher, it must meet the following requirements for input entities:

* One input must be a [TextActionEntity](/uwp/api/windows.ai.actions.textactionentity) with the name `agentName`.  

* One input must be a **TextActionEntity** with the name `prompt`.  

* All input combinations for the action must accept both the `agentName` and `prompt` entities.

* Invoking the App Action should open an application where the user can actively interact with the agent, not just complete work in the background.

```csharp
using Microsoft.AI.Actions.Annotations;
using System.Threading.Tasks;
using Windows.AI.Actions;

namespace ZavaAgentProvider
{
    [ActionProvider]
    public sealed class ZavaAgentActionsProvider
    {
        [WindowsAction(
            Id = "ZavaAgentAction",
            Description = "Start an agent for Zava",
            Icon = "ms-resource://Files/Assets/ZavaLogo.png",
            UsesGenerativeAI = true
        )]
        [WindowsActionInputCombination(
            Inputs = ["agentName", "prompt"],
            Description = "Start Zava Agent with '${agentName.Text}'."
        )]
        [WindowsActionInputCombination(
            Inputs = ["agentName", "prompt", "attachedFile"],
            Description = "Start Zava Agent with '${agentName.Text}' and additional context."
        )]

        public async Task StartZavaAgent(
            [Entity(Name = "agentName")] string agentName,
            [Entity(Name = "prompt")] string prompt,
            [Entity(Name = "attachedFile")] FileActionEntity? attachedFile,
            InvocationContext context)
        {
            // Your agent invocation logic here
            await InvokeAgentAsync(agentName, prompt, attachedFile);
        }

        public async Task InvokeAgentAsync(string agentName, string prompt, FileActionEntity? attachedFile)
        {
            // Process the agent invocation with the provided inputs
            if (attachedFile != null)
            {
                await Task.Run(() => $"Starting agent '{agentName}' with prompt '{prompt}' and file context");
            }
            else
            {
                await Task.Run(() => $"Starting agent '{agentName}' with prompt '{prompt}'");
            }
        }
    }
}
```

The following example shows the action definition file generated from the action provider class definition shown in the previous example.

```json
{ 
  "version": 3,  
  "actions": [  
    {  
      "id": "ZavaAgentAction",  
      "description": "Start an agent for Zava",  
      "icon": "ms-resource://Files/Assets/ZavaLogo.png",  
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
          "description": "Start Zava Agent with '${agentName.Text}'."  
        },  
        {  
          "inputs": [ "agentName", "prompt", "attachedFile" ],  
          "description": "Start Zava Agent with '${agentName.Text}' and additional context."  
        }  
      ],  
      "outputs": [],  
      "invocation": {  
        "type": "Uri",  
        // Example of a valid launch URI using the inputs defined above   
        "uri": "zavaLaunch:invokeAgent?agentName=${agentName.Text}&prompt=${prompt.Text}&context=${attachedFile.Path}"  
      }  
    }  
  ]  
} 
```

## Test your App Action

Before registering your action as an Agent Launcher, verify that your App Action works correctly. Follow the testing guidance in the [Get started with App Actions on Windows](../app-actions/actions-get-started.md) article to ensure your action:

1. **Registers successfully** - Verify that your action appears in the action catalog.
1. **Accepts the required inputs** - Test that your action can receive the `agentName` and `prompt` text entities.
1. **Invokes correctly** - Confirm that your action logic executes when invoked.
1. **Handles optional inputs** - If you have optional inputs like `attachedFile`, test both with and without them.

You can test your App Action by using the Windows.AI.Actions APIs or the the [App Actions Testing Playground app](../app-actions/actions-test-tool.md). Once you've confirmed your App Action works as expected, you can proceed to register it as an agent launcher.

## Create an agent definition JSON file

Create a new JSON file in your project's `Assets` folder (or your preferred location) to define your agent registration. The agent definition links your agent to the App Action you created in the previous step.

The value of the **action_id** field in the agent definition manifest must match the **id** field specified in the action definition manifest for an action included in the same app package.

```json
{ 
  "manifest_version": "0.1.0", 
  "version": "1.0.0", 
  "name": "Zava.ZavaAgent", 
  "display_name": "ms-resource://zavaAgentDisplayName", 
  "description": "ms-resource://zavaAgentDescription", 
  "icon": "ms-resource://Files/Assets/ZavaLogo.png", 
  "action_id": "ZavaAgentAction"
} 
```

Set the JSON file to **Copy to Output Directory** in your project properties:

- **For C# projects**: Right-click the JSON file in Solution Explorer, select **Properties**, and set **Copy to Output Directory** to **Copy if newer** or **Copy always**.
- **For C++ projects**: Add the following code to your project file:

```xml
<Content Include="Assets\agentRegistration.json">
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</Content>
```


## Static registration via app package manifest

Agent Launchers can register statically (at install time) or dynamically (at runtime). This section covers static registration.

The Package.appxmanifest file provides the details of the MSIX package for an app. If you followed the get started tutorial for app actions, you already added a [uap3:Extension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element to register the action by setting the extension **Name** attribute to `com.microsoft.windows.ai.actions`. To register the action as an Agent Launcher, you need to add another app extension with the **Name** set to `com.microsoft.windows.ai.appAgent`. Under the **Properties** element of the app extension element, you must provide a **Registration** element that specifies the location of your agent definition JSON file.

> [!NOTE]
> Each statically registered Agent Launcher should have its own AppExtension entry.

```xml
<uap3:Extension Category="windows.appExtension"> 
    <uap3:AppExtension 
      Name="com.microsoft.windows.ai.appAgent" 
      Id="ZavaAgent" 
      DisplayName="Zava Agent" 
      PublicFolder="Assets"> 
        <uap3:Properties> 
          <Registration>agentRegistration.json</Registration> 
        </uap3:Properties> 
    </uap3:AppExtension> 
</uap3:Extension> 
```


## Dynamic registration via On Device Registry (ODR)

In addition to static registration, you can register Agent Launchers dynamically at runtime by using the Windows On Device Registry (ODR). This method is useful when you need to add or remove agents based on application logic.

### Add an Agent Launcher dynamically

Use the `odr add-app-agent` command to register an Agent Launcher dynamically. This command takes the path to your agent registration JSON file.

```csharp
using System.Diagnostics;

// Create process start info
ProcessStartInfo startInfo = new ProcessStartInfo
{
    FileName = "odr.exe",
    Arguments = $"add-app-agent \"<path to agentDefinition.json>\"",
    UseShellExecute = false,
    RedirectStandardOutput = true,
    RedirectStandardError = true,
    CreateNoWindow = true
};

// Start the ODR process
using Process process = new Process { StartInfo = startInfo };
process.Start();

// Read the output
string output = await process.StandardOutput.ReadToEndAsync();
string error = await process.StandardError.ReadToEndAsync();

await process.WaitForExitAsync();
```

The command returns a JSON response with the following structure:

```json
{
  "success": true,
  "agent": {
    "id": "ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent",
    "version": "1.0.0",
    "name": "Zava.ZavaAgent",
    "display_name": "Zava Agent",
    "description": "Description for Zava agent.",
    "icon": "C://pathToZavaIcon.png",
    "package_family_name": "ZavaPackageFamilyName",
    "action_id": "ZavaAgentAction"
  },
  "message": "Agent registered successfully"
}
```

### Remove an Agent Launcher dynamically

Use the `odr remove-app-agent` command to remove a dynamically registered Agent Launcher. You can only remove agents that the same package adds dynamically.

```csharp
using System.Diagnostics;

// Create process start info
ProcessStartInfo startInfo = new ProcessStartInfo
{
    FileName = "odr.exe",
    Arguments = $"remove-app-agent \"ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent\"",
    UseShellExecute = false,
    RedirectStandardOutput = true,
    RedirectStandardError = true,
    CreateNoWindow = true
};

// Start the ODR process
using Process process = new Process { StartInfo = startInfo };
process.Start();

// Read the output
string output = await process.StandardOutput.ReadToEndAsync();
string error = await process.StandardError.ReadToEndAsync();

await process.WaitForExitAsync();
```

The command returns:

```json
{
  "success": true,
  "message": "Agent removed successfully"
}
```

> [!IMPORTANT]
> Due to package identity requirements, you can't use `add-app-agent` and `remove-app-agent` from an unpackaged app. You must run these commands from within a packaged application that also contains the associated App Action.

## List available Agent Launchers

To discover all registered Agent Launchers on the system, use the `odr list-app-agents` command. This command returns all Agent Launchers that you can invoke.

```powershell
odr list-app-agents
```

This command returns a JSON array of agent definitions:

```json
{
  "agents": [
    {
      "id": "ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent",
      "version": "1.0.0",
      "name": "Zava.ZavaAgent",
      "display_name": "Zava Agent",
      "description": "Description for Zava agent.",
      "icon": "C://pathToZavaIcon.png",
      "package_family_name": "ZavaPackageFamilyName",
      "action_id": "ZavaAgentAction"
    }
  ]
}
```

Use the `package_family_name` and `action_id` fields together to identify and invoke the associated App Action.

## Invoke an Agent Launcher

To invoke an Agent Launcher, follow these steps:

1. Call `odr list-app-agents` to get all available Agent Launchers.
1. Select the agent you want to invoke based on your application logic (for example, user interaction).
3. Use the Windows.AI.Actions APIs to invoke the agent's associated App Action

Here's an example of invoking an Agent Launcher by using the Windows.AI.Actions APIs:

```csharp
using Windows.AI.Actions;
using System.Threading.Tasks;

public async Task InvokeAgentLauncherAsync(string packageFamilyName, string actionId, string agentName, string prompt)
{
    // Get the action catalog
    var catalog = ActionCatalog.GetDefault();
    
    // Get all actions for the package
    var actions = await catalog.GetAllActionsAsync();
    
    // Find the specific action by package family name and action ID
    var targetAction = actions.FirstOrDefault(a => 
        a.PackageFamilyName == packageFamilyName && 
        a.Id == actionId);
    
    if (targetAction != null)
    {
        // Create the input entities
        var entityFactory = new ActionEntityFactory();
        var agentNameEntity = entityFactory.CreateTextEntity(agentName);
        var promptEntity = entityFactory.CreateTextEntity(prompt);
        
        // Create input dictionary
        var inputs = new Dictionary<string, ActionEntity>
        {
            { "agentName", agentNameEntity },
            { "prompt", promptEntity }
        };
        
        // Invoke the action
        await targetAction.InvokeAsync(inputs);
    }
}
```

## Test your Agent Launcher

After you verify the functionality of your App Action, test your Agent Launcher registration and invocation.

### Test static registration

1. Build and deploy your packaged application with the agent extension in the manifest.
1. Open a terminal and run:
   ```powershell
   odr list-app-agents
   ```
1. Verify that your Agent Launcher appears in the output with the correct `package_family_name` and `action_id`.

### Test dynamic registration

1. Run the `odr add-app-agent` command from within your packaged application as shown in the dynamic registration section.
1. Check the command output to confirm successful registration.
1. Verify the registration by running:
   ```powershell
   odr list-app-agents
   ```
1. Confirm your agent appears in the list.
1. Test removal by running the `odr remove-app-agent` command with your agent's ID.
1. Confirm removal by running `odr list-app-agents` again and verifying the agent no longer appears.

### Test Agent Launcher invocation

After you register your Agent Launcher, test the end-to-end invocation:

1. Run `odr list-app-agents` to get your agent's `package_family_name` and `action_id` values.
1. Use the App Action testing approach from the [Get started with App Actions](../app-actions/actions-get-started.md) article or the Action Test Tool to invoke your action with the required `agentName` and `prompt` inputs.
1. Verify that your app receives the inputs correctly and your agent logic executes as expected.
1. Test optional inputs like `attachedFile` if your action supports them.
