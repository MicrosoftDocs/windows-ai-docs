---
title: Agent Session
description: Learn about the MCP agent session
ms.date: 10/31/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Agent Session

> [!WARNING]
> DOC STATUS : First draft with initial approval

On Windows, MCP flows enjoy a higher level of security, identity and isolation thanks to the concept of an agent session. 

## Overview

The agent session provides a dedicated environment for AI agents to run their code inside of. This is powered by Windows accounts, and works by providing a new Windows account to the AI agent when it tries to run an MCP server. 

TODO: Architecture diagram and explanation of pieces

## What a server can and cannot do in the agent session

When designing an MCP server to run in the agent session, the key concepts are that it has its own identity, its own account, and its own environment. 

Therefore, an agent session MCP server can do these things:

- Access the user's files by requesting access from the user
- Access its own files 
- Run executables for the user by requesting permission to do so
- Run executables in its own environment

Without an agent session, MCP servers are run directly as an executable in the user's account. This setup has a variety of security and access problems which agent sessions solve. The list below shows things that an agent session **cannot do** which could happen by running an MCP server directly:

- Impersonate the user
    - e.g: Send an email as the user, access account details and settings as the user
- Have full access to the user's files, apps, and desktop
    - Without user permission be able to access sensitive files, open and interact with applications, and change the user's state and settings

## Supported capabilities of the agent session

Today the supported capabilities of an agent session are:

- Access the user's files...
- TODO: Finish list

## What types of MCP servers are supported in an agent session

Agent sessions are on by default for the user, and an MCP server will run inside of it if it meets these criteria:

- Has identity on Windows
- Has a `manifest` with full tag details (_meta etc.)
    - TODO: Need to determine exactly what tags are needed / how to phrase this

Please see the [MCP with Windows identity guide](./mcp-windows-identity.md) for info on how to build a server to meet this.

Servers which are packaged with MCP bundles are not supported in agent session today, you can see the [MCP bundle dev guidance](./mcp-mcpb.md) for more info on those servers.

## Bypass mode

TODO: Verify the name of this feature

You can disable the agent session feature in Windows and allow all MCP servers to run as direct executables in the user's system by going to Windows Settings -> Advanced Settings -> Enable Bypass mode

TODO: Verify the location of that setting page. 