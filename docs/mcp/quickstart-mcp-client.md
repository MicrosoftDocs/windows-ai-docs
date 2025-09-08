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

For development purposes:

- Set up a ge_current_directwinpd_uxip VM
- Install in your VM
    - Latest Node, Git for Windows and .NET runtime 9
- Enable velocity key 58763365 using stagingtool or ixptools Set-DeviceVelocityKey
- Git clone WMSS and open `src/components/wmss/wmss.sln`
    - `git clone https://microsoft@dev.azure.com/microsoft/OS.Developer/_git/asklar`
- Build and deploy `wmss.exe` to your VM
- On the VM, figure out where the EXE got deployed, it should be somewhere under `c:\users\<youruserid>\AppData\Local\DevelopmentFiles`
- Import `wmss.reg` TODO: Where does this come from?
- Update the Path value under `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Mcp` to the path of the WMSS.exe on your VM

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