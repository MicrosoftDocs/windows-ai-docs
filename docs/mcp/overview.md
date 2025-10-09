---
title: Model Context Protocol (MCP) for Windows overview
description: Learn what MCP is and how Windows integrates discovery, consent, and enterprise controls to make MCP servers easy to build and ship.
ms.date: 08/12/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft with initial approval

# Model Context Protocol (MCP) for Windows overview

Model Context Protocol (MCP) standardizes how apps expose tools and context to AI agents. Think of it like a USB‑C port for AI: MCP clients (agents, IDEs) can “plug in” to MCP servers (your apps) in a consistent way.

Windows includes built-in support that makes MCP easy to productize:
- Discovery: Apps register MCP servers once; clients discover them via the Windows MCP Registry.
- Consent: Windows prompts users when clients enumerate servers or invoke tools.
- Enterprise: Admins can set policy for which clients can call which servers, with auditing and logging. 

## Why use MCP on Windows

- Better trust and control: Users see clear prompts and have a central place to manage AI connectors; enterprises can enforce policy and collect logs.
- Simpler distribution: No per-client Docker/pip/npm setup. Install your app and Windows handles discovery.
- Works with many clients: Claude Desktop, VS Code, Visual Studio, and any MCP-capable app.

## How it works

1) Your app ships (or includes) an MCP server binary (exe) and a registration file.
2) Windows registers the server (use MSIX manifest for packaged apps, or MCP bundles for unpackaged options).
3) An MCP client requests access to enumerate servers; the user consents.
4) The client reads your server’s capabilities and invokes tools with user approval.

## These docs focus on *registering* MCP servers on Windows

MCP servers are their own deep technology stack. To avoid duplication, these docs focus just on how to register MCP servers on Windows and interact with them.

These docs assume you already know what an MCP server is, what an MCP client is, and how to build and develop for them. You can learn about these concepts [at the main MCP docs](https://modelcontextprotocol.io/docs/getting-started/intro).

## Next steps

- [Register an MCP server on Windows](./quickstart-mcp-server.md)
- [Make an MCP client on Windows](./quickstart-mcp-client.md)
