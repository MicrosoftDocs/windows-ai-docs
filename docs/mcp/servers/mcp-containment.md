---
title: Securing agent connectors on Windows
description: Learn about securely containing agent connectors and MCP servers on Windows
ms.date: 10/31/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Securely containing MCP servers on Windows

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

By default, [MCP servers accessed through the Windows on-device agent registry](../quickstart-mcp-host.md) are securely contained in a separate environment and can only access approved resources, limiting vulnerability to threats like cross-prompt injection attacks.

## Overview

MCP servers that are contained will run in a separate Windows session using a separate agent user account.

This includes all servers provided by Windows and MCP servers that are registered with the Windows on-device agent registry using MSIX packages or packages with external locations. [Learn more about packaging MCP servers with MSIX](./mcp-server-overview.md).

Servers which are packaged with MCP bundles currently cannot run contained. See [Register an MCP server with an MCP bundle](./mcp-mcpb.md) to learn more.

## Requirements 

To run contained, servers must have:

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

See [Package your MCP server with MSIX](./mcp-windows-identity.md) for info on how to build a server to meet these requirements.

## What a server can and cannot do when contained

When designing an MCP server, the key concepts are that it has its own identity, and runs run in a separate Windows session using a separate agent user account.

Therefore, a server does not have direct access to the user's session, including:
- user files (unless the user grants permission to the agent)
- user settings, registry, and credentials
- apps and windows the user is using
- running executables that modify the user session

However, a server running in the agent session can:
- Access the internet
- Read and write to the agent's files and registry
- Run executables in the agent user environment
- Run executables in the agent session
- Access certain user files with the user's consent

## Requesting access to user files

While running in the agent session, an MCP server will not have access to the user files by default. However, an MCP server can request access to user files by declaring the `broadFileSystemAccess` in their package appxmanifest.xml. Once *any* server with this capabilitiy is used by a host, the user will be prompted to grant access to their files, for that host, and any server will have be able to access those files.

> [!NOTE]
> Permissions to user files are granted for the host and not per server. When the user grants access to their user files, any MCP server used in that session will have access to the user's files. 

## Reducing protections for agent connectors

Some MCP servers may not operate correctly when contained. This includes MCP bundles (.mcpb), which currently cannot run securely contained.      

For testing purposes, you can enable this setting in Windows Settings to enable MCP servers accessed through the Windows on-device agent registry to run with reduced protections:

> [!IMPORTANT]
> This setting enables MCP servers to run with more access and privileges, and may expose your device to additional security threats.

`Settings > System > Advanced > AI components > Reduce protections for agent connectors`
