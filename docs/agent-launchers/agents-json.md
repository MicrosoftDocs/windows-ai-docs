---
title: Agent definition JSON schema for Agent Launchers on Windows
description: Describes the format of the agent definition JSON file for registering Agent Launchers on Windows.
ms.topic: reference
ms.date: 02/04/2025
ms.localizationpriority: medium
---

# Agent definition JSON schema for Agent Launchers on Windows

This article describes the format of the agent definition JSON file for Agent Launchers on Windows. This file must be included in your project with the **Build Action** set to "Content" and **Copy to Output Directory** set to "Copy if newer". Specify the package-relative path to the JSON file in your package manifest XML file.

An Agent Launcher registration links an agent to an App Action that handles the agent invocation. For information on creating the App Action, see [Get started with Agent Launchers on Windows](agents-get-started.md).

## Example agent definition JSON file

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

## Agent definition JSON properties

The table below describes the properties of the agent definition JSON file.

### Document root

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| manifest_version | string | The schema version of the agent definition manifest. Current version is "0.1.0". | Yes |
| version | string | The version of your agent. Use semantic versioning (e.g., "1.0.0"). | Yes |
| name | string | A unique identifier for your agent, typically using reverse domain notation (e.g., "Zava.ZavaAgent"). This value is not localizable and must be unique within your package. | Yes |
| display_name | string | The user-facing display name for the agent. This value is localizable using the `ms-resource://` format to reference a string resource in your app package. | Yes |
| description | string | A user-facing description of what the agent does. This value is localizable using the `ms-resource://` format to reference a string resource in your app package. | Yes |
| icon | string | The icon for the agent. This value is localizable using the `ms-resource://` format to reference an icon resource deployed with your app package. | Yes |
| action_id | string | The identifier of the App Action that will handle invocations of this agent. This must match the `id` field of an action defined in the same app package. For information on creating the associated App Action, see [Get started with Agent Launchers on Windows](agents-get-started.md). | Yes |

## Localization

The `display_name`, `description`, and `icon` properties support localization through the `ms-resource://` URI scheme. This allows you to provide localized strings and resources for different languages.

### String resources

To localize string properties, use the following format:

```json
"display_name": "ms-resource://resourceName"
```

The resource name corresponds to a string resource defined in your app package's resource files (`.resw` files for C# projects, or `.rc` files for C++ projects).

### Icon resources

To localize icon properties, use the following format:

```json
"icon": "ms-resource://Files/Assets/iconName.png"
```

The path is relative to your package root and can reference different icons for different languages through your app's resource system.

## Relationship to App Actions

Each Agent Launcher must reference an App Action through the `action_id` property. The App Action defines how the agent is invoked, including:

- Required input entities (`agentName` and `prompt`)
- Optional input entities (such as `attachedFile`)
- The invocation mechanism (URI activation or COM)

The App Action and Agent Launcher must be in the same app package. When an Agent Launcher is invoked, the system uses the `action_id` to locate the corresponding App Action and invokes it with the appropriate inputs.

For detailed information on creating the App Action for your Agent Launcher, see [Get started with Agent Launchers on Windows](agents-get-started.md).

## Related articles

- [Get started with Agent Launchers on Windows](agents-get-started.md)
- [Agent Launchers on Windows Overview](index.md)
- [Get started with App Actions on Windows](../app-actions/actions-get-started.md)
