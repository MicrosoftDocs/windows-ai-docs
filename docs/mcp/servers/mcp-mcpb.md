---
title: Register an MCP server with an MCP bundle
description: Learn how to register an MCP server on Windows using an MCP bundle
ms.date: 11/11/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server with an MCP bundle

This article will walk through the process of registering an MCP server using an MCP bundle, a package format that standardize deployment for MCP servers. For more information on this package format, see the MCP bundle github repo, [github.com/anthropics/mcpb/](https://github.com/anthropics/mcpb/) are  You can include an MCPB into your existing Windows app, or you can distribute the MCPB on its own.

Apps that are packaged using the MSIX package format can include metadata in their package that will automatically register the MCP server when the package is installed. For more information see, [Register an MCP server from an app with package identity](mcp-windows-identity.md).

> [!WARNING]
> MCP servers run in an agent session that runs under its own user account. MCP bundles are not supported in the agent session, and are therefore not supported by default. Servers registered using MCP bundles will not be accessible from the Windows on-device agent registry. For testing purposes, you can enable MCP bundles for agent sessions. For more information including important security caveats, see [Reducing protections for agent connectors](./mcp-containment.md#reducing-protections-for-agent-connectors).

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Windows build 26220.7262 or higher 
- Developer mode enabled. For more information, see [Developer Mode features and debugging](/windows/apps/get-started/developer-mode-features-and-debugging).
- NodeJS installed. To install NodeJS using WinGet, use the following command:
	- `winget install OpenJS.NodeJS`.NodeJS`

## Install the MCP bundle tooling package

Open the command line and install the NPM package with this command:

```powershell
npm install -g @anthropic-ai/mcpb
```

## Build your MCP Bundle

You can initialize and build your bundle by running `mcpb init` and `mcpb pack`. For more information, see the [For Bundle Developers](https://github.com/anthropics/mcpb?tab=readme-ov-file#for-bundle-developers) on the MCPB github repo.

You can also use the [MCP server C# sample](https://github.com/microsoft/mcp-on-windows-samples/tree/main/mcp-server-csharp) to generate a MCP bundle to test with using the following commands:

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
cd mcp-on-windows-samples
cd mcp-server-csharp
.\build-mcpb.ps1
```

These commands will create a file named `mcp-dotnet-mcpb-server.mcpb` in the `mcp-server-csharp` folder.

## Distribute your `.mcpb` file or include it as part of your app

You can distribute the `.mcpb` file directly, as-is, to ship a standalone MCP server or you can integrate it as part of your application by executing the `.mcpb` bundle file during install of your application.
 
## Test your MCP server

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. For more information, see [Testing MCP servers on Windows](test-mcp-server.md).

## Next steps

- Learn about the features of the agent session for MCP for Windows and understand why you might want to move your app to use MSIX and package identity in order to use the agent session. For more information, see [Securely containing MCP servers on Windows](./mcp-containment.md).
