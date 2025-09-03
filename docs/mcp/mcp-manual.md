---
title: Register an MCP server manually
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft (Barely)

# Register an MCP server manually

You can interact directly with the Windows MCP server server (WMSS) to register or unregister MCP servers. This is a manual process and we recommend using the [identity](./mcp-windows-identity.md) or [MCP bundle](./mcp-mcpb.md) guides instead where possible. 

- TODO: When would I want to use this? Seems like only if I was making my own custom installer and didn't want to use MCPB

## Pre-requisites

- Be on Windows build TODO-AddBuild or higher 
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./build-mcp-server.md)
- Have WMSS installed (TODO: Validate this assumption)

## Register a server

```powershell
wmss.exe --register <path-to-dxt-file>
```

TODO: Needs detail

## Unregister a server

```powershell
wmss.exe --unregister <id>
```

TODO: Needs detail