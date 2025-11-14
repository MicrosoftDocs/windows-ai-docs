---
title: Quickstart MCP host on Windows
description: Get started quickly with Model Context Protocol (MCP) on Windows for a host
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Quickstart: MCP host on Windows

This article shows how an MCP host app can list, connect to, and interact with the MCP servers registered on Windows using the [Windows odr.exe on-device agent registry tool](./odr-tool.md) `odr.exe`. This walkthrough will use a sample host app from the MCP on Windows samples repo, [github.com/microsoft/mcp-on-windows-samples](https://github.com/microsoft/mcp-on-windows-samples).

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Windows build 26220.7262 or higher
- An MCP host app with package identity. For more information on package identity, see [An overview of Package Identity in Windows apps](/windows/apps/desktop/modernize/package-identity-overview). Package identity is granted to apps that are packaged using the MSIX package format. For more information, see [What is MSIX?](/windows/msix/overview).
    - **Note** This requirement is not enforced in the public preview release but it will be in the stable release.

## Clone the sample

Clone the [MCP on Windows host sample](https://github.com/microsoft/mcp-on-windows-samples/tree/main/mcp-client-js) to your device and navigate to it:

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
cd mcp-on-windows-samples/mcp-client-js
```

## Set up and build the sample

Run these commands: 

```powershell
npm install
npm run start
```

The tool will present you with a command line UI that lets you interact with the MCP servers registered on your device. The following sections will show the Javascript code used by the tool to implement various features of an MCP host app.

## List available MCP servers

List the available MCP servers executing the command line call `odr.exe list`. This command returns the list of servers in JSON format, which is stored and used in subsequent examples:

```js
const { stdout, stderr } = await execFileAsync('odr.exe', ['list']);

if (stderr) {
    console.error('Warning:', stderr);
}

const servers = JSON.parse(stdout);
```

## Connect to an MCP server 

Connect to one of the available MCP servers by getting the command and arguments from the JSON returned in the previous step. Create a `StdioClientTransport`, passing in the command and arguments. Create a new `Client` object. Call **connect** to connect to the MCP server.

```js
const command = server.manifest?.server?.mcp_config?.command;
const args = server.manifest?.server?.mcp_config?.args || [];

if (!command) {
    throw new Error('Server configuration missing command.');
}

// Create MCP client with stdio transport
// Set stderr to 'ignore' to silence server info logs
const transport = new StdioClientTransport({
    command: command,
    args: args,
    stderr: 'ignore'
});

const client = new Client({
    name: 'mcp-client',
    version: '1.0.0'
}, {
    capabilities: {}
});

// Connect to the server
await client.connect(transport);
```

## List tools from a server

Call `listTools` to list the tools that are registered by the MCP server.

```js


// List available tools
const toolsResponse = await client.listTools();
const tools = toolsResponse.tools || [];
```

## Calling a tool

Each MCP tool has a name and an optional set of parameters. The `gatherToolParameters` function in the sample will help gather input parameters, and then you can call the tool directly:

```js
const parameters = await gatherToolParameters(tool); // This function is from the sample code

const result = await client.callTool({
    name: tool.name,
    arguments: parameters
});
```


## Next Steps

- Learn how to build and register an MCP server that can be discovered and used by a host app. For more information, see [Registering an MCP server](./servers/mcp-server-overview.md).