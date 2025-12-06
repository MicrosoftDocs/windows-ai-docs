---
title: Model Context Protocol (MCP) on Windows overview
description: Learn what MCP is and how Windows integrates discovery, consent, and enterprise controls to make MCP servers easy to build and ship.
ms.date: 12/06/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Model Context Protocol (MCP) on Windows

> [!NOTE]
> **Some information relates to prereleased product that might change substantially before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

MCP on Windows provides the Windows On-device Agent Registry (ODR), a secure, manageable interface to discover and use agent connectors from local apps and remote servers using [Model Context Protocol (MCP)](https://modelcontextprotocol.io). MCP is an open protocol designed to standardize integrations between AI apps and external tools and data sources. By using MCP on Windows to discover and interact with MCP servers, app developers can enhance the capabilities of AI models, enabling them to produce more accurate, relevant, context-aware responses, and to expand the tasks they can complete for users.

## About the ODR

The Windows ODR enables apps and agents to securely discover and access MCP servers on Windows. Benefits of using the registry include:

* **Broad, standardized MCP support:** the registry supports MCP servers from local apps and remote servers
* **Discoverability:** agents can easily find and connect to all available MCP servers
* **Security:** MCP servers are [contained in a separate environment](servers/mcp-containment.md) by default and can only access approved resources, limiting vulnerability to threats like cross-prompt injection attacks
* **User and admin control**: users and IT admins can control access to MCP servers for each agent by using Windows Settings and management tools like Microsoft Intune
* **Logging and auditability**: users and administrators can log and audit interactions between MCP clients and servers
* **Windows connectors**: Windows includes default connectors for agents to use, including a [File Explorer MCP server](file-connector.md)

For information about registering MCP servers with Windows ODR, see [Register an MCP server on Windows](./servers/mcp-server-overview.md).

The Windows ODR also provides a command line tool, `odr.exe`, that enables users and developers to view and manage MCP servers. For more information, see: [The Windows on-device agent registry command-line tool (odr.exe)](./odr-tool.md).

## MCP agent connectors on Windows

An agent connector is a packaged integration point that allows an AI agent to connect to external capabilities through the Model Context Protocol (MCP). It acts as the bridge between the agent and an MCP server, enabling the agent to discover and use tools, resources, and prompts exposed by that server. Windows File Explorer implements an MCP connector that provides a set of tools to access and modify files using a Model Context Protocol (MCP) server. For more information see [Windows File Explorer MCP connector](file-connector.md).

Several agents that support using MCP servers on Windows are currently available, including:

* [Windows Settings connector](/windows/apps/develop/windows-integration/settings-mcp)
* [Visual Studio GithHub Copilot agent mode](/visualstudio/ide/copilot-agent-mode)
* [Visual Studio Code GitHub Copilot agent mode](https://code.visualstudio.com/docs/copilot/chat/copilot-chat)

There are frameworks that enable you to build your own agents that use the Windows ODR to access MCP servers, including:

* [Microsoft Agent Framework](/agent-framework/overview/agent-framework-overview) - a development kit for building AI agents and agent workflows

The Windows File Explorer MCP connector integrates MCP server tools into the context menus for working with files and folders in File Explorer. For more information, see [Windows File Explorer MCP connector](file-connector.md).

For more information and examples of creating a host app that uses MCP to list, connect to, and interact with the MCP servers registered on Windows, see: [Quickstart: MCP host on Windows](./quickstart-mcp-host.md).


## Next steps

* [Create an MCP server on Windows](servers/mcp-server-overview.md) 
* [Test your MCP server](servers/test-mcp-server.md) 
* [Create an MCP host on Windows](./quickstart-mcp-host.md)
