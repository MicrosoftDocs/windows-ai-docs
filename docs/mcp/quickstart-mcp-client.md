---
title: MCP for Windows quick start guide for clients
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First initial rough draft

An MCP client lists, connects to and interacts with different MCP servers. This doc shows how you can accomplish those tasks with the MCP servers registered on Windows by interacting with the On Device Registry executable `odr.exe`.

## Pre-requisites

- Be on Windows build TODO-Get-This-number

## List available MCP servers

Run: 

```powershell
odr.exe list
```

This outputs a list of servers like so:

```json
[
 {
    "id": "MicrosoftWindows.Client.Core...",
    "manifest": {
        ....
      }
    }
  },
  {
    "id": "MicrosoftWindows.Client.Core...",
    "manifest": {
        ....
    }
  },
  ....
]
```

TODO: Code sample for calling this dynamically? Does it need an API? Needs a sample IMO

## Connect to an available MCP server

Each MCP server listed above includes a command on how to run it. For example one might look like so:

```json
{
    "id": "MicrosoftWindows.Core...",
        "manifest": {
            .....
            "server": {
            "type": "binary",
            "entry_point": "C:\\windows\\system32\\shellhost.exe",
            "mcp_config": {
                "command": "odr.exe",
                "args": [
                "mcp",
                "--proxy",
                "MicrosoftWindows.Core..."
                ]
            }
        }
    }
},
```

You can then connect to it by executing that command. This also works with different MCP client SDKs, for example with NodeJS:

```js
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
    
    await client.connect(transport);
    
    // List available tools
    const toolsResponse = await client.listTools();
    const tools = toolsResponse.tools || [];
```

## Next steps

- [Test your MCP server with our testing instructions](./test-mcp-server.md)
- [Understand the permissions story for MCP servers when deploying them](./permissions.md)