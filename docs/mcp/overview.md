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

- Simpler distribution: No per-client Docker/pip/npm setup. Install your app and Windows handles discovery.
- Better trust and control: Users see clear prompts; enterprises can enforce policy and collect logs.
- Works with many clients: Claude Desktop, VS Code, and any MCP-capable app.

## How it works

1) Your app ships (or includes) an MCP server binary and a registration file.
2) Windows registers the server (via app manifest for packaged apps or via sparse/unpackaged options).
3) An MCP client requests access to enumerate servers; the user consents.
4) The client reads your server’s capabilities and invokes tools with user approval.

## Next steps

- [Quickstart](./quickstart.md): Add the manifest/AppExtension and register your server.

TODO: Add links to Quickstart, Architecture, Identity, and FAQ once published paths are finalized.