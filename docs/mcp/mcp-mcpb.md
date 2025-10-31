---
title: Register an MCP server with an MCP bundle
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server with an MCP bundle

> [!WARNING]
> DOC STATUS : First draft with initial approval

[MCP bundles](https://github.com/anthropics/mcpb/) are a package format that standardize deployment for MCP servers. You can include an MCPB into your existing Windows app, or you can distribute the MCPB on its own.

## Pre-requisites

- Be on Windows build TODO-AddBuild or higher 
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./quickstart-mcp-client.md)
- Have NodeJS installed
    - You can install with winget by running: `winget install OpenJS.NodeJS`

> [!WARNING]
> MCP bundles are not supported in the agent session, and are therefore not supported by default. To use one please [enable the bypass setting, instructions here](./agent-session.md#bypass-mode)

## Install the MCP bundle tooling package

Open the command line and install the NPM package with this command:

```powershell
npm install -g @anthropic-ai/mcpb
```

TODO - Validate this command is correct.

## Build your MCP Bundle

You can initialize and build your bundle by running `mcpb init` and `mcpb pack`, please [see the MCP bundle docs](https://github.com/anthropics/mcpb?tab=readme-ov-file#for-bundle-developers) for more information on this step.


## Distribute your `.mcpb` file, or include it as part of your app install

Either you can stop here and distribute your `.mcpb` file directly, as is, to ship a standalone MCP server.

Or you can integrate it as part of your application by running the `.mcpb` bundle to install and uninstall at install or uninstall of your app.

- TODO: What are the exact commands? `.mcpb /quiet` and `.mcpb /uninstall`? 

- TODO: nuance between signed binary + Trusted Launch path, vs. untrusted path
 
## Test your MCP server

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 

If all tests look good, then you are finished!

## Next steps

- Understand the [agent session on Windows](./agent-session.md) to understand how and why you might want to move your MCP server to use the agent session.
