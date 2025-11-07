---
title: Quickstart MCP host on Windows
description: Get started quickly with Model Context Protocol (MCP) on Windows for a host
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Quickstart: MCP host on Windows

An MCP host can list, connect to and interact with MCP servers. This doc shows how you can accomplish those tasks with the MCP servers registered on Windows using the [Windows on-device agent registry command-line tool](./odr-tool.md) `odr.exe`.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-Get-This-number
- Your MCP host application will need to have Windows identity
    - This is not enforced today in the public preview but will be in the future

## Clone the sample

Clone the [MCP on Windows host sample](https://github.com/microsoft/mcp-on-windows-samples/tree/main/mcp-client-js) to your device and navigate to it:

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
cd mcp-on-windows-samples/mcp-client-js
```

This quickstart will walk you through the sample and its key concepts. 

## Set up and build the sample

Run these commands: 

```powershell
npm install
npm run start
```

And then interact with the sample to try out manually calling an MCP tool from the command line. 

Now that you've run the sample, let's examine how it works.

## Listing available MCP servers

First you can list what MCP servers are available by using an API to run `odr.exe list` and storing its JSON output:

```js
const { stdout, stderr } = await execFileAsync('odr.exe', ['list']);

if (stderr) {
    console.error('Warning:', stderr);
}

const servers = JSON.parse(stdout);
```

## Listing tools from a server

Then for a given server, you can use the MCP SDK to connect to it and list its tools.

First we connect to the server by runnings its command:

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
```

And then list its tools, which is stored as a JSON object:

```js
// Connect to the server
await client.connect(transport);

// List available tools
const toolsResponse = await client.listTools();
const tools = toolsResponse.tools || [];
```

## Calling a tool

Tools will have a name, and an optional set of parameters. The `gatherToolParameters` function in the sample will help gather input parameters, and then you can call the tool directly:

```js
const parameters = await gatherToolParameters(tool); // This function is from the sample code

const result = await client.callTool({
    name: tool.name,
    arguments: parameters
});
```

Now you have a fully functioning MCP client that can access different servers and run their tools.

## Next Steps

- If you'd like to build your own MCP server that could show up in this list, see the [MCP server quickstart](./servers/mcp-server-overview.md).