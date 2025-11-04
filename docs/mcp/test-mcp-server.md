---
title: Test MCP server
description: Test your MCP server implementation on Windows
ms.date: 08/12/2025
ms.topic: concept-article
keywords: MCP, Model Context Protocol, Windows, architecture, design, implementation
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Test MCP servers on Windows

See how you can test your MCP server both without and with agents, and making sure that they are registered correctly in Windows.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Test that the MCP functions work correctly

Once your MCP binary is built, you can test out its functionality using the MCP inspector. You will need to have Node installed to use this. Open PowerShell and run this command:

```powershell
npx @modelcontextprotocol/inspector <path to your .exe>
```

This will open a web browser, and you can manually test the functionality of your server.

## Test that the MCP server has been registered on Windows correctly

Once your MCP server is part of a Windows app and is registered on Windows, you can test that it was registered successfully using this command in PowerShell:

```powershell
odr.exe list
```

If your server shows up correctly in that list, then it is registered!

## Do a full end to end test with VS Code

Open VS Code, and install the MCP extension (TODO: Add link). 

TODO: Add photo of extension.

Once installed, open the AI chat Window and click on "List tools" to see what servers and tools are available.

TODO: Add photo

Ensure that the tool is shown there and is checked for use, and then ask a question to Copilot in agent mode where it would want to use your tool. You can use this method to verify your full scenario as a user would use it.

TODO: Add photo

