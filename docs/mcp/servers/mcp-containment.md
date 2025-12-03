---
title: Securely containing MCP servers on Windows
description: Learn about securely containing MCP servers and agent connectors on Windows
ms.date: 11/11/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Securely containing MCP servers on Windows

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

By default, MCP servers accessed through the Windows on-device agent registry (ODR) are run in an agent session, securely contained in a separate environment and can only allowed access to approved resources, limiting vulnerability to threats like cross-prompt injection attacks. This article describes security requirements for MCP servers to run in an agent session, the access restrictions for agent sessions, and how to grant agents access to additional resources.

## Overview

MCP servers that are contained run in a separate Windows session using a separate agent user account. This includes all servers provided by Windows and all MCP servers that are registered with the Windows on-device agent registry using MSIX packages or packages with external locations. For information about server registration, see [Register an MCP server on Windows](./mcp-server-overview.md).

Servers which are packaged with MCP bundles currently cannot run contained in an agent session. For more information about registering MCP servers using MCP bundles, see [Register an MCP server with an MCP bundle](./mcp-mcpb.md).

## Requirements 

To run contained, MCP servers must meet the following requirements:

- The server must be implemented as a binary (.exe) server
- The server must have package identity and be registered via an MSIX package extension
- The server must provide a valid manifest.json registration that includes, at a minimum, the following fields:
    - manifest_version
    - name
    - version
    - description
    - author
    - server
    - _meta (with a `com.microsoft.windows` field containing a definition for both `static_responses` and `tools/list`).

For information about packaging an MCP server to meet these requirements, see [Register an MCP server from an app with package identity](./mcp-windows-identity.md).

## What a server can and can't do when contained

A contained MCP server has its own identity and runs run in a separate Windows session using a separate agent user account. Therefore, a server does not have direct access to the user's session, including:

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

While running in the agent session, an MCP server will not have access to the user files by default. However, an MCP server can request access to user files by declaring the specific known folder capabilities in their package appxmanifest.xml. A host app is an app that the user interacts with directly and acts as a proxy between the user and one or more MCP servers. IF an MCP server that requests a particular capability is used by a host, the user will be prompted to grant access to the associated resource. Once the user grants access, all MCP servers used by that host app will have access to the resource. For example, if one MCP server used by the host requests user file access and the access is granted, any MCP server used by the same host app will be able to access the user files.

> [!NOTE]
> Permissions to user files are granted for the host and not per server. When the user grants access to their user files, any MCP server used in that session will have access to the user's files. 

## Reducing protections for agent connectors

Any packaged apps with identity will always run in a contained session in this preview. 

Unpackaged applications, including MCP bundles (.mcpb files) will not be able to run in containment. For testing purposes, you can enable this setting in Windows Settings to enable MCP servers accessed through the Windows on-device agent registry to run with reduced protections:

> [!IMPORTANT]
> This setting enables MCP servers to run with more access and privileges, and may expose your device to additional security threats.

`Settings > System > Advanced > AI components > Reduce protections for agent connectors`

To make your MCP server run in the user session, currently in the public preview you would need to deploy it as an unpackaged application.
