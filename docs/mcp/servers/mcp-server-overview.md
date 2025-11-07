---
title: Registering an MCP server
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server on Windows

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

Learn how to register your MCP server with the Windows on-device agent registry.

Registering your MCP server enables agents to easily discover and connect to it, provides greater security for your users and services, and enables additional management when your server is used in enterprise environments.

This article provides guidance on registering an MCP server on Windows. If you prefer to use the provided samples directly, you can skip the registration steps and proceed to the explanations below.

## Prerequisites

- Windows build TODO-Add-this-number or higher

## Building an MCP server

Many development tools and frameworks support creating MCP servers, including:

* [MCP C# SDK](https://learn.microsoft.com/dotnet/ai/get-started-mcp#develop-with-the-mcp-c-sdk) - an SDK for building MCP clients and servers for .NET apps and libraries
* [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) - an SDK for building MCP clients and servers using TypeScript

Information on additional SDKs is available here: https://modelcontextprotocol.io/docs/sdk.

## Choosing how to register your MCP server with Windows

Once you've built an MCP server, you can register it with Windows in several ways. The method you choose depends on how your application is packaged and deployed:

* **Applications with [package identity](https://learn.microsoft.com/windows/apps/desktop/modernize/package-identity-overview) via [MSIX](https://learn.microsoft.com/windows/msix/) or a [package with external location](https://learn.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps-overview):**  
   Use MSIX's built-in MCP integration to streamline your development process. [Learn more](./mcp-windows-identity.md).

* **Applications without identity (e.g., MSI, exe) or existing MCP bundles:**  
   - Add Windows identity to your application to reduce dependency requirements. [Learn more](./mcp-windows-identity.md#pre-requisites).  
   - Package your MCP server within an **MCP bundle (.mcpb)** and include it in your app. [Learn more](./mcp-mcpb.md).  
      > **Note:** Currently, MCP bundles cannot run [securely contained](./mcp-containment.md) and will not be accessible from the Windows on-device agent registry unless users explicitly enable the option to "Reduce protections for agent connectors" in Windows Settings.
      
 * **Remote MCP servers or manual registration scenarios:**  
   If you use a different installation technology or want to dynamically decide whether to make your MCP server available, you can [manually register your server using the Windows on-device agent registry command-line tool](./mcp-manual.md).
