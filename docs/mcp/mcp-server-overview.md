---
title: Register an MCP server with MCP for Windows
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server with MCP for Windows

Learn how to register your MCP server on Windows. 

## Prerequisites

- Windows build TODO-Add-this-number or higher

## Building an MCP server

These docs focus on _registering_ an MCP server on Windows. The samples will provide ready-made examples that will show the needed steps to enable your MCP server to register with Windows. You can skip this step and proceed to the explanations below if you'd just like to use the provided samples.

> [!NOTE]
> To build the MCP server into your application head over to the modelcontextprotocol repository on GitHub.  
>
>- [C# SDK](https://github.com/modelcontextprotocol/csharp-sdk?tab=readme-ov-file#getting-started-server)
>- [Typescript](https://github.com/modelcontextprotocol/typescript-sdk?tab=readme-ov-file#quick-start)
>
>See more info on [SDKs here](https://modelcontextprotocol.io/docs/sdk).

## Choosing how to register your MCP server with Windows

There are a few different options on how you can register your MCP server with Windows, depending on how your application is deployed and whether it has identity. 

This guide will help you choose an option:

- Do you have an app with idenity (via MSIX or sparse packaging)? Or do you plan to release a server that runs in [agent session](./agent-session.md)?
   - [Fast track your development with MSIX's built in MCP integration](./mcp-windows-identity.md)
- Do you have an app without identity (MSI, exe, etc.) or an existing MCP bundle?
   - [Add Windows identity to your application and use its identity to minimize dependency requirements](./mcp-windows-identity.md#pre-requisites)
   - [Package your MCP server within an MCP bundle and include it in your app](./mcp-mcpb.md) Note that MCP bundles cannot run [in the agent session](./agent-session.md) today.
- Do you have a remote MCP server or plan to manually run registration (e.g. you are using a different install technology, or want to dynamically decide whether to make your MCP server available)?:
   - [Register your server manually with the ODR CLI](./mcp-manual.md)
