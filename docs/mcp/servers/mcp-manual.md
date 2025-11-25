---
title: Manually register remote and local MCP servers
description: Learn how to manually register remote and local MCP servers
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Manually register remote and local MCP servers

This article describes how to register and unregister MCP servers by interacting directly with the Windows On-Device Registry (ODR). Manual registration is required to register remote MCP servers. Manual registration also enables fine-grained control when registering a local MCP server. For typical scenarios we recommend registering local MCP servers using the automatic registration features of packaged apps or, for non-packaged apps, including an MCP bundle in your app installed. For more information, see [Register an MCP server from an app with package identity](./mcp-windows-identity.md) or [Register an MCP server with an MCP bundle](./mcp-mcpb.md) guides instead where possible. 

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Windows build 26220.7262 or higher

## The odr.exe command line interface

`odr.exe` is the CLI that enables MCP servers to be listed, registered, and interacted with on Windows.  For more information about this tool, see the [Windows odr.exe on-device agent registry tool](../odr-tool.md).

## Register a server

If you have a remote MCP server you can register with the following command:

```powershell
odr.exe mcp add --uri <url-to-your-server>
```

If you have a mcpb manifest JSON file you can register a local server with the following command:

```powershell
odr.exe mcp add <path-to-mcpb-manifest-json>
```

For more information on the format of the `manifest.json` file, see the MCP bundle github repo, [github.com/anthropics/mcpb/](https://github.com/anthropics/mcpb/).

## List installed servers

The following command outputs a list of all MCP servers that are registered on the machine.

```powershell
odr.exe list
``` 


## Unregister a server

The following command unregisters an MPC server.
```powershell
odr.exe mcp remove <mcp_server_name>
```

This unregisters a specific server.

