---
title: Agent Session
description: Learn about the MCP agent session
ms.date: 10/31/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Agent Session

On Windows, MCP flows enjoy a higher level of security, identity and isolation thanks to the concept of an agent session. 

## Overview

The agent session provides a dedicated environment for AI agents to run their code inside of. This is powered by Windows accounts, and works by providing a new Windows account to the AI agent when it tries to run an MCP server. By default, only servers that are intended to run in the agent session will be available for use. All other servers will not be available unless the user disables (TODO: wording)

TODO: Architecture diagram and explanation of pieces

## Requirements for running in agent session


All servers packaged in an MSIX package will run in the agent session. All other servers (MCPB packaged or loose registration) can only run in the user session and will require the user to disable MCP server protections in order to run (TODO: wording).

| Server Registration | Agent Session | User Session |
|---------------------|---------------|--------------|
| MSIX                | X             |              |
| MCPB                |               | X            |
| Loose               |               | X            |
| Remote              |               |              |

Requirements:
- A binary (exe) server
- Registered via an MSIX package extension
- Valid manifest.json registration, inlcuding at minimum:
    - manifest_version
    - name
    - version
    - description
    - author
    - server
    - `_meta` with `com.microsoft.windows` definition (including `static_responses` and `tools/list`) 

See the [Package your MCP server with MSIX](./mcp-windows-identity.md) for info on how to build a server to meet these requirements.

Servers which are packaged with MCP bundles are not supported in agent session today, you can see the [MCP bundle dev guidance](./mcp-mcpb.md) for more info on those servers.

## What a server can and cannot do in the agent session

When designing an MCP server to run in the agent session, the key concepts are that it has its own identity, and runs in a seperate session with its own account and environment. 

Therefore, a server running in the agent session does not have access to the user's session, including:
- user files (unless the user grants permission to the agent)
- user settings, registry, and credentials
- apps and windows the user is using
- running executables that modify the user session

However, a server running in the agent session can:
- Access the internet
- Read and write to the agent's files and registry
- Run executables in the agent session


## Request access to user files

While running in the agent session, an MCP server will not have access to the user files by default. However, an MCP server can request access to user files by declaring the `broadFileSystemAccess` in their package appxmanifest.xml. Once *any* server with this capabilitiy is used by a host, the user will be prompted to grant access to their files, for that host, and any server will have be able to access those files.

TODO: snippet of how to add the capability

> [!NOTE]
> Permissions to user files are granted for the host and not per server. When the user grants access to their user files, any MCP server used in that session will have access to the user's files. 



## Bypass mode

TODO: Verify the name of this feature

You can disable the agent session feature in Windows and allow all MCP servers to run as direct executables in the user's system by going to Windows Settings -> Advanced Settings -> Enable Bypass mode

TODO: Verify the location of that setting page. 