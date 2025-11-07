---
title: Register an MCP server manually
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server manually

You can interact directly with the Windows On-Device Registry (ODR) to register or unregister MCP servers. This is best used for remote servers and if you have an included binary for your MCP server we recommend using the [identity](./mcp-windows-identity.md) or [MCP bundle](./mcp-mcpb.md) guides instead where possible. 

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-AddBuild or higher (TODO include velocity keys)
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./quickstart-mcp-client.md)

## The on device registry

`odr.exe` is the CLI that powers how MCP servers are listed, registered, and interacted with on Windows. 

See the Windows On-Device Registry CLI information for full info on what is available with this package. TODO: Add link

## Register a server

```powershell
odr.exe register <path-to-mcpb-manifest-json>
```

This command requires a [valid `manifest.json` file](https://github.com/anthropics/mcpb/blob/main/MANIFEST.md).

## List installed servers

```powershell
odr.exe list
``` 

This outputs a list of all MCP servers that are registered on the machine.

## Unregister a server

```powershell
odr.exe unregister <mcp_server_name>
```

This unregisters a specific server.

