---
title: Register an MCP server with an MCP bundle
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft

# Register an MCP server with an MCP bundle

MCP bundles (TODO Add link) are a package that allows MCP servers to be shipped and installed as standalone packages. You can integrate a DXT into your existing app, or you can distribute it on its own.

## Pre-requisites

- Be on Windows build TODO-AddBuild or higher 
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./build-mcp-server.md)
- Have NodeJS installed (TODO: Validate this assumption. Is it NodeJS? Will there be other SDKs?)

## Install the DXT tooling package

Open the command line and install the NPM package with this command:

```powershell
npm install -g @anthropic-ai/mcpb
```

TODO - Validate this command is correct.

## Initialize your configuration

Navigate to your project and create a manifest.json:

```powershell
mcpb init
```

Follow the steps provided to add the required information for your `manifest.json` file. 

## Bundle your MCP server

Run:

```powershell
mcpb pack
```

This will produce a `.mcpb` file which will container your bundled MCP server.

## Sign your package

Sign your package with this command:

```powershell
mcpb sign <path-to-mcpb-file>
```

- TODO: How do they get the `.cer` file for this signing?

## Distribute your `.mcpb` file, or include it as part of your app install

Either you can stop here and distribute your `.mcpb` file directly, as is, to ship a standalone MCP server.

Or if you would like to integrate it as part of an existing app, you can include install and uninstall commands for this in your application.

- TODO: What are the exact APIs to install this? Or uninstall it? How does a dev do that?