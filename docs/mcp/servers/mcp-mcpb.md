---
title: Register an MCP server with an MCP bundle
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server with an MCP bundle

[MCP bundles](https://github.com/anthropics/mcpb/) are a package format that standardize deployment for MCP servers. You can include an MCPB into your existing Windows app, or you can distribute the MCPB on its own.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-AddBuild or higher 
- Enable developer mode
- Have NodeJS installed
    - You can install with winget by running: `winget install OpenJS.NodeJS`

> [!WARNING]
> MCP bundles are not supported in the agent session, and are therefore not supported by default. To use one [enable the bypass setting, instructions here](./agent-session.md#bypass-mode)

## Install the MCP bundle tooling package

Open the command line and install the NPM package with this command:

```powershell
npm install -g @anthropic-ai/mcpb
```

## Build your MCP Bundle

You can initialize and build your bundle by running `mcpb init` and `mcpb pack. See the [MCP bundle documentation](https://github.com/anthropics/mcpb?tab=readme-ov-file#for-bundle-developers) for more information on this step.

You can also use a [MCP bundle sample](https://github.com/microsoft/mcp-on-windows-samples/tree/main/mcp-server-csharp) to generate a MCP bundle to test with:

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
cd mcp-on-windows-samples
cd mcp-server-csharp
.\build-mcpb.ps1
```

This will create a `.mcpb` file in your folder.

## Distribute your `.mcpb` file, or include it as part of your app

Either you can stop here and distribute your `.mcpb` file directly, as is, to ship a standalone MCP server.

Or you can integrate it as part of your application by running the `.mcpb` bundle during install of your application. Currently in public preview just install is added and planned in the future is a `silent` and `uninstall` option. 
 
## Test your MCP server

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 

If all tests look good, then you are finished!

## Next steps

- Understand the [agent session on Windows](./agent-session.md) to understand how and why you might want to move your MCP server to use the agent session.
