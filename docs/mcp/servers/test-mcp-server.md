---
title: Test MCP servers on Windows
description: Learn how to test an MCP server implementation on Windows
ms.date: 08/12/2025
ms.topic: concept-article
keywords: MCP, Model Context Protocol, Windows, architecture, design, implementation
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Test MCP servers on Windows

This article describes several ways that you can test and validate the registration and functionality of your MCP server.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Test that the MCP functions work correctly

After building your MCP server into a binary executable, you can test out its functionality using the MCP inspector, a browser-based interface for interacting with an MCP server. This tool requires that NodeJS is installed on your device. To install NodeJS using WinGet, use the following command:
	- `winget install OpenJS.NodeJS`

To run the tool, open PowerShell and run this command:

```powershell
npx @modelcontextprotocol/inspector <path to your .exe>
```

This will open a page in your web browser that allows you to manually test the functionality of your server.

## Test that the MCP server has been registered on Windows correctly

Once your MCP server is part of a Windows app and is registered on Windows, you can test that it was registered successfully using this command in PowerShell:

```powershell
odr.exe list
```

If your server shows up correctly in that list, then it has been registered successfully.


