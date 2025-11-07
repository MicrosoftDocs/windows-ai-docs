---
title: Model Context Protocol (MCP) for Windows overview
description: Learn what MCP is and how Windows integrates discovery, consent, and enterprise controls to make MCP servers easy to build and ship.
ms.date: 08/12/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Agent connectors: Model Context Protocol (MCP) and the Windows on-device agent registry

> [!NOTE]
> **Some information relates to pre-released product which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

Agent connectors help agents on Windows connect to tools and other agents to better complete tasks for users.

You can create an agent connector by using [Model Context Protocol (MCP)](https://modelcontextprotocol.io) to expose your app's unique functionality to other agents.

Agents on Windows can use connectors to expand the tasks they can complete for users. Agents can access connectors using the **Windows on-device agent registry**, which provides a secure, manageable interface to discover and use agent connectors from local apps and remote servers using MCP.

## What is Model Context Protocol (MCP)?

The [Model Context Protocol (MCP)](https://modelcontextprotocol.io) is an open protocol designed to standardize integrations between AI apps and external tools and data sources. By using MCP, developers can enhance the capabilities of AI models, enabling them to produce more accurate, relevant, and context-aware responses.

## The Windows on-device agent registry

The Windows on-device agent registry enables agents to securely discover and access MCP servers on Windows.

Benefits of using the registry include:

* **Broad, standardized MCP support:** the registry supports MCP servers from local apps and remote servers
* **Discoverability:** agents can easily find and connect to all available MCP servers
* **Security:** MCP servers are [contained in a separate environment](servers/mcp-containment.md) by default and can only access approved resources, limiting vulnerability to threats like cross-prompt injection attacks
* **User and admin control**: users and IT admins can control access to MCP servers for each agent using Windows Settings and management tools like Microsoft Intune
* **Logging and auditability**: interactions between MCP clients and servers are logged and auditable by users and administrators
* **Windows connectors**: Windows includes default connectors for agents to use, including a [File Explorer MCP server](servers/file-connector.md)

The Windows on-device agent registry also provides an `odr.exe` commandline tool for users and developers to view and manage MCP servers. For more information see: [Windows on-device agent registry command-line tool](./odr-tool.md).

## Using MCP agent connectors on Windows

Various existing agents support using MCP servers on Windows, including:

* [Visual Studio GithHub Copilot agent mode](https://learn.microsoft.com/visualstudio/ide/copilot-agent-mode)
* [Visual Studio Code GitHub Copilot agent mode](https://code.visualstudio.com/docs/copilot/chat/copilot-chat)

You can also build your own agents that use the Windows on-device registry to access MCP servers, for example using:

* [Microsoft Agent Framework](https://learn.microsoft.com/agent-framework/overview/agent-framework-overview) - a development kit for building AI agents and agent workflows

For more information and examples of accessing agent connectors using MCP, see: [Quickstart: MCP host on Windows](./quickstart-mcp-host.md).


## Next steps

* [Create an MCP server on Windows](servers/mcp-server-overview.md) 
* [Test your MCP server](servers/test-mcp-server.md) 
* [Create an MCP host on Windows](./quickstart-mcp-host.md)
