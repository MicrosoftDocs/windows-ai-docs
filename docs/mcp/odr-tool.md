---
title: The Windows on-device agent registry command-line tool (odr.exe)
description: Learn about the Windows odr.exe on-device agent registry tool
ms.date: 10/31/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP]
---

# The Windows on-device agent registry command-line tool (odr.exe)

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

The Windows on-device agent registry command-line tool `odr.exe` provides discovery and management tools for on-device agents in Windows, including registered [MCP servers](./overview.md).

## Syntax

```cmd
odr [command] [options]
```

### Parameters

| Command | Description |
| ------- | ----------- |
| mcp | commands related to Model Context Protocol servers |

| Option | Description |
| ------- | ----------- |
| -?, -h, --help | Show help and usage information |
| --version | Show version information |
| --verbose | Enable verbose logging |

### MCP Commands

```cmd
odr mcp [command] [options]
```

| Command | Description |
| ------- | ----------- |
| run | Run MCP server |
| list | List registered MCP servers |
| add `<manifest file path>` | Register an MCP server |
| remove `<server id>` | Unregister an MCP server |
| configure `<server id>` | Configure an MCP server |





