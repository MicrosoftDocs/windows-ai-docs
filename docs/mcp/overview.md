---
title: Model Context Protocol (MCP) for Windows overview
description: Learn what MCP is and how Windows integrates discovery, consent, and enterprise controls to make MCP servers easy to build and ship.
ms.date: 08/12/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Model Context Protocol (MCP) for Windows overview

Learn how to build MCP tools into your Windows applications.

## What is Model Context Protocol (MCP)?

Model Context Protocol (MCP) is an open standard that enables AI applications to securely connect to contextual data sources. It provides a standardized way for AI models to access real-time information from files, databases, applications, and system resources while maintaining security and privacy. MCP acts as a universal translator between AI applications and diverse data ecosystems. MCP for Windows is a framework that makes it easy to integrate MCP servers into a unified Windows experience, allowing users and other apps to engage with your MCP app or service seamlessly.

These docs focus on how to **register MCP servers on Windows and interact with them** and will use technical terms about MCP such as MCP servers, clients, hosts, etc. 

[Want to learn more about MCP? Visit the MCP docs to discover more.](https://modelcontextprotocol.io/docs/getting-started/intro)

## MCP on Windows features

Windows offers an easy way for you to add MCP capabilities to your applications using the on device registry which gives these features to your end users without you having to write any extra code:

- **Trust and control on which MCP servers to run:** Users see clear prompts and have a central place to manage AI connectors; enterprises can enforce policy and collect logs.
- **MCP servers are automatically installed when installing aWIndows app:** No per-client Docker/pip/npm setup. Users only install your MCP server once and Windows handles discovery.
- **Integration with any MCP host:**  Visual Studio Code, Visual Studio, Claude Desktop, or any MCP-capable app automatically and easily can discover newly installed MCP servers.

These capabilities are powered by an 'agent session' on Windows, which allows the AI agent to run with its own identity and security posture. The [agent session docs page](./agent-session.md) goes into more detail about this feature.

## MCP for Windows lifecycle

1) Your app ships (or includes) an MCP server binary (exe) and a registration file.
2) Your user installs your app and your MCP Server gets registered with Windows
3) The MCP clients on the user's computer requests access to enumerate servers; the user consents.
4) The MCP client reads your server’s capabilities and invokes tools with user approval.

## Next steps

- [Learn how to register an MCP server with Windows](./quickstart-mcp-server.md)
- [Make an MCP client on Windows](./quickstart-mcp-client.md)
