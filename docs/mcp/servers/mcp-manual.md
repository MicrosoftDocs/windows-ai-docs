---
title: Register remote MCP servers or manual registration
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register a remote MCP server or manually register a server

You can interact directly with the Windows On-Device Registry (ODR) to register or unregister MCP servers. This is best used for remote servers and if you have an included binary for your MCP server we recommend using the [identity](./mcp-windows-identity.md) or [MCP bundle](./mcp-mcpb.md) guides instead where possible. 

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-AddBuild or higher

## The on device registry

`odr.exe` is the CLI that powers how MCP servers are listed, registered, and interacted with on Windows. 

See the [Windows On-Device Registry CLI information](../odr-tool.md) for full info on what is available with this package.

## Register a server

If you have a mcpb manifest JSON you can register with this command

```powershell
odr.exe mcp add <path-to-mcpb-manifest-json>
```

And if you have a remote MCP server you can register with this command

```powershell
odr.exe mcp add --transport http --uri <url-to-your-server>
```

This command requires a [valid `manifest.json` file](https://github.com/anthropics/mcpb/blob/main/MANIFEST.md).

## List installed servers

```powershell
odr.exe list
``` 

This outputs a list of all MCP servers that are registered on the machine.

## Unregister a server

```powershell
odr.exe mcp remove <mcp_server_name>
```

This unregisters a specific server.

