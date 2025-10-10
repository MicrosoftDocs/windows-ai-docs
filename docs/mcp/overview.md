---
title: Model Context Protocol (MCP) for Windows overview
description: Learn what MCP is and how Windows integrates discovery, consent, and enterprise controls to make MCP servers easy to build and ship.
ms.date: 08/12/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Model Context Protocol (MCP) for Windows overview

> [!WARNING]
> DOC STATUS : First draft with initial approval

Model Context Protocol (MCP) is an open standard that enables AI applications to securely connect to contextual data sources. It provides a standardized way for AI models to access real-time information from files, databases, applications, and system resources while maintaining security and privacy. MCP acts as a universal translator between AI applications and diverse data ecosystems. MCP for Windows is a framework that makes it easy to integrate MCP servers into a unified Windows experience, allowing users and other apps to engage with your MCP app or service seamlessly.

## MCP on Windows features

- **Better trust and control:** Users see clear prompts and have a central place to manage AI connectors; enterprises can enforce policy and collect logs.
- **Simpler distribution:** No per-client Docker/pip/npm setup. Users only install your MCP server once and Windows handles discovery.
- **Works with any MCP client:**  Visual Studio Code, Visual Studio, Claude Desktop, or any MCP-capable app.

## MCP for Windows lifecycle

1) Your app ships (or includes) an MCP server binary (exe) and a registration file.
2) Your user installs your app and your MCP Server gets registered with Windows
3) The MCP clients on the user's computer requests access to enumerate servers; the user consents.
4) The MCP client reads your server’s capabilities and invokes tools with user approval.

## Learning more about MCP servers

MCP servers are their own deep technology stack. You can learn about these concepts [at the main MCP docs](https://modelcontextprotocol.io/docs/getting-started/intro).

These docs focus just on how to **register MCP servers on Windows and interact with them** and assume you already know what an MCP server is, what an MCP client is, and how to build and develop for them. 

## Next steps

- [Learn how to register an MCP server with Windows](./quickstart-mcp-server.md)
- [Make an MCP client on Windows](./quickstart-mcp-client.md)
