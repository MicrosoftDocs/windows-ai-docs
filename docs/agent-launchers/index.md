---
title: Agent Launchers on Windows overview
description: Learn about Agent Launchers on Windows, a framework that allows Windows apps to implement and register AI-based agents that can be invoked to perform tasks autonomously.
ms.topic: overview
ms.date: 1/05/2025
dev_langs:
- csharp
---

# Agent Launchers on Windows overview

Agent Launchers on Windows provides a standardized way for apps to register AI agents and make them discoverable across the system. This enables users to access agents from any supporting experience, such as from the Start menu, search, or within applications, without needing to know which app provides each agent.

## What is an Agent Launcher?

An Agent Launcher is a registered entry point for an AI agent on Windows. Without Agent Launchers, each experience would need custom integration code for every agent—whether through Model Context Protocol (MCP), App Actions, or proprietary APIs. Agent Launchers solve this by providing a unified registration and discovery mechanism where apps register their agents once, making them available to all supporting experiences.

## What is an agent?

In the context of Agent Launchers, agents are AI-powered assistants designed for active, ongoing conversations that help users accomplish complex tasks. They are more than chatbots or one-off request processors:

- **Interactive and conversational**: Engage in multi-turn dialogues, asking clarifying questions and providing contextual responses
- **Task-oriented**: Help users complete specific goals, from planning trips to analyzing data to creating content
- **Contextually aware**: Understand and maintain context throughout conversations, remembering previous interactions
- **Action-capable**: Take actions on behalf of users and integrate with app functionality to get things done
- **Visible and accessible**: Open a user interface where users can actively interact, see progress, and guide their work

Agent Launchers are designed for agents that provide interactive experiences where users and AI collaborate, not for background services or silent automation.

## Benefits of using Agent Launchers?

### For users

- **Unified discovery**: Find all available agents from any supporting experience without remembering which app contains which agent.
- **Seamless integration**: Access agents from different contexts, including from the Start menu, search, or within other applications.
- **Consistent experience**: Interact with agents through consistent, familiar patterns regardless of the provider.

### For developers

- **Single integration**: Register your agent once and make it available to all supporting experiences.
- **Flexible deployment**: Register agents statically at install time or dynamically at runtime based on authentication, subscriptions, or other conditions.
- **Ecosystem reach**: Leverage the standardized App Actions framework to tap into a growing ecosystem.

### For experiences and platforms

- **Easy discovery**: Query the On-Device Registry (ODR) to find all registered agents on the system.
- **Reliable invocation**: Launch agents through a standardized mechanism with well-defined inputs.
- **No custom integrations**: Support all agents without app-specific code.

## How Agent Launchers work

Agent Launchers are built on the Windows App Actions framework. An Agent Launcher consists of:

* **Agent definition manifest**: A JSON file with metadata including display name, description, unique identifier, and the app action to invoke
* **App extension declaration**: An entry in the app package manifest that registers the agent with Windows
* **App Action with required entities**: An App Action with required `agentName` and `prompt` inputs, plus optional entities like `attachedFile`

Agents are registered with and retrieved through the On-Device Registry (ODR) via the `odr.exe` command-line tool. Registration can be static (at install time) or dynamic (at runtime). When invoked, the system locates the associated App Action and launches it with the user's prompt and context, opening the agent's interface for interaction.

## Get started

To learn how to create an Agent Launcher for your Windows app, see [Get started with Agent Launchers on Windows](agents-get-started.md).

For detailed information about the agent definition JSON schema, see [Agent definition JSON schema](agents-json.md). 

