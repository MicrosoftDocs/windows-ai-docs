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

* **For C# projects**: Right-click the JSON file in Solution Explorer, select **Properties**, and set **Copy to Output Directory** to **Copy if newer** or **Copy always**.
* **For C++ projects**: Add the following code to your project file:

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

Use the `odr agent-info add` command to register an Agent Launcher dynamically. This command takes the path to your agent registration JSON file.

```csharp
using System.Diagnostics;

// Create process start info
ProcessStartInfo startInfo = new ProcessStartInfo
{
    FileName = "odr.exe",
    Arguments = $"agent-info add \"<path to agentDefinition.json>\"",
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
  "agent_id": "ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent",
  "extended_error": 0
}
```

### Remove an Agent Launcher dynamically

Use the `odr agent-info remove` command to remove a dynamically registered Agent Launcher. You can only remove agents that the same package adds dynamically.

```csharp
using System.Diagnostics;

// Create process start info
ProcessStartInfo startInfo = new ProcessStartInfo
{
    FileName = "odr.exe",
    Arguments = $"agent-info remove \"<path to agentDefinition.json>\"",
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
  "extended_error": 0
}
```

> [!IMPORTANT]
> Due to package identity requirements, you can't use `agent-info add` and `agent-info remove` from an unpackaged app. You must run these commands from within a packaged application that also contains the associated App Action.

## List available Agent Launchers

To discover all registered Agent Launchers on the system, use the `odr agent-info list` command. This command returns all Agent Launchers that you can invoke.

```powershell
odr agent-info list
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

### Supply Icon Qualifiers

When querying for available Agent Launchers using `odr agent-info list`, you can supply one or more additional arguments to specify qualifiers for the icon resources that are returned in the command output. The following table lists the icon qualifiers that can be used when listing Agent Launchers.

| Qualifiers | Description |
|------------|-------------|
| contrast | Controls high‑contrast mode selection. Valid values: `standard`, `high`, `black`, `white` |
| language | Specifies the user‑preferred language used to resolve localized resources, for example, "en-US". Only a single value can be specified. |
| scale  | Indicates the display scale factor used to choose the correctly sized assets, for example, 100, 200, 400 |
| targetsize | Specifies the pixel side‑length for icons, for example, 16, 24. This is primarily used for file‑type or protocol icons. |
| theme | Determines app theme influence, for example, `light`, `dark`. The system automatically infers the theme value unless it is overridden by supplying this qualifier. |

You can specify multiple qualifiers in a call to `odr agent-info list`. You can supply multiple values for each qualifier by using a comma as a delimiter. If malformed qualifiers are present in the query, the system will make a best effort to return results based on the well-formed qualifiers. If the system can't resolve any icons, the returned icon list will be empty.

The following example demonstrates a call to `odr agent-info list`, specifying qualifiers for a single `scale` value and multiple `theme` values.

```powershell
odr agent-info list --theme dark,light --scale 200
```

This command returns he following JSON payload which includes the `icons` array of the available icons that match the specified icon qualifiers.

```json
{
  "agents": [
    {
      "id": "ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent",
      "version": "1.0.0",
      "name": "Zava.ZavaAgent",
      "display_name": "Zava Agent",
      "description": "Description for Zava agent.",
      "package_family_name": "ZavaPackageFamilyName",
      "action_id": "ZavaAgentAction",
      "icons": [
        {
          "src": "C:\\Users\\[user name]\\AppData\\Local\\ZavaAgent\\Assets\\AgentIcon.scale-200_theme-dark.png",
          "theme": "dark",
          "scale": "200"
        },
        {
          "src": "C:\\Users\\[user name]\\AppData\\Local\\ZavaAgent\\Assets\\AgentIcon.scale-200_theme-light.png",
          "theme": "light",
          "scale": "200"
        }
      ]
    }
  ]
}
```

## Invoke an Agent Launcher

To invoke an Agent Launcher, follow these steps:

1. Call `odr agent-info list` to get all available Agent Launchers.
1. Select the agent you want to invoke based on your application logic (for example, user interaction).
1. Use the Windows.AI.Actions APIs to invoke the agent's associated App Action

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
   odr agent-info list
   ```

1. Verify that your Agent Launcher appears in the output with the correct `package_family_name` and `action_id`.

### Test dynamic registration

1. Run the `odr agent-info add` command from within your packaged application as shown in the dynamic registration section.
1. Check the command output to confirm successful registration.
1. Verify the registration by running:

   ```powershell
   odr agent-info list
   ```

1. Confirm your agent appears in the list.
1. Test removal by running the `odr agent-info remove` command with your agent's ID.
1. Confirm removal by running `odr agent-info list` again and verifying the agent no longer appears.

### Test Agent Launcher invocation

After you register your Agent Launcher, test the end-to-end invocation:

1. Run `odr agent-info list` to get your agent's `package_family_name` and `action_id` values.
1. Use the App Action testing approach from the [Get started with App Actions](../app-actions/actions-get-started.md) article or the Action Test Tool to invoke your action with the required `agentName` and `prompt` inputs.
1. Verify that your app receives the inputs correctly and your agent logic executes as expected.
1. Test optional inputs like `attachedFile` if your action supports them.

## Handle errors

The success status of `odr agent-info` commands is returned in the JSON output in the `extended_error` field. A value of zero indicates success and a non-zero value is an extended error code that specifies the type of error that occurred. The `message` field contains a human-readable string describing the error. The `message` field is only included in the output for commands that are unsuccessful.

The following is the example output of a call to `odr agent-info remove` where the operation failed because the specified agent was not found.

```json
{
  "extended_error": -2147023728,
  "message": "E_NOTFOUND Agent not found: ZavaAgent_cw5n1h2txyewy_Zava.ZavaAgent"
}
```

Error codes that can be returned from `odr agent-info` commands include the following.

| Error code | Value | Description |
|------------|-------|-------------|
|S_OK|0x00000000|Success.|
| E_FAIL | 0x80004005 | Generic error. |
| E_INVALIDARG | 0x80070057 | Invalid argument. Examples include invalid JSON path being passed as input for registration or attempting to register an agent that is already registered. |
| E_ACCESSDENIED | 0x8007000 | Access denied. |
| E_NOTFOUND | 0x80070490 | Agent being unregistered is not in the registry. |
| E_ABORT | 0x80004004 | The call was canceled before the operation was completed. |
