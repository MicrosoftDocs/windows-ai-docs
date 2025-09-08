---
title: MCP for Windows quick start guide for clients
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First initial rough draft

An MCP client lists, connects to and interacts with different MCP servers. This doc shows how you can accomplish those tasks with the MCP servers registered on Windows.

## Pre-requisites

- Be on Windows build TODO-Get-This-number

### Internal only and temporary pre-reqs

- Get a copy of `wmss.exe`
    - TODO - for now check [here](https://microsoft.visualstudio.com/OS/_git/OSClient?%2FSrc%2FComponents%2FWMSS%2Fwmss.md=&path=/Src/Components/WMSS/WMSS.md)

## List available MCP servers

Run: 

```powershell
wmss.exe list
```

This outputs a list of servers like so:

```
TODO: Sample output
```

TODO: Code sample for calling this dynamically? Does it need an API?

## Connect to an available MCP server

TODO - Verify this

From the outputted JSON, there are commands to connect to the servers. Run one of those commands.

TODO - How to connect this via the MCP SDK? Need a more comprehensive E2E story here