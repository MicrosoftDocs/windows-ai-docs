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

You can interact directly with the Windows On-Device Registry (ODR) to register or unregister MCP servers. This is a manual process and we recommend using the [identity](./mcp-windows-identity.md) or [MCP bundle](./mcp-mcpb.md) guides instead where possible. 

See the Windows On-Device Registry CLI information for more info on what is available with this package. TODO: Add link

- TODO: When would I want to use this? Seems like only if I was making my own custom installer and didn't want to use MCPB

## Pre-requisites

- Be on Windows build TODO-AddBuild or higher (TODO include velocity keys)
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./build-mcp-server.md)

## Register a server

```powershell
odr.exe register <path-to-mcpb-manifest-json>
```

TODO: Needs detail

## List installed servers

```powershell
odr.exe list
``` 

TODO: Needs detail
## Unregister a server

```powershell
wmss.exe unregister <mcp_server_name>
```

