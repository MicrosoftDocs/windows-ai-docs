---
title: MCP servers on Windows overview
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: overview
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# MCP servers on Windows overview

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

This section provides information about MCP server registration on Windows. Registering your MCP server enables agents on Windows to easily discover and connect to your server, provides greater security for your users and services, and enables additional management when your server is used in enterprise environments.

For information about building an MCP server, look at one of the available SDKs including:

* [MCP C# SDK](/dotnet/ai/get-started-mcp#develop-with-the-mcp-c-sdk) - an SDK for building MCP clients and servers for .NET apps and libraries
* [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) - an SDK for building MCP clients and servers using TypeScript

Information on additional SDKs is available here: https://modelcontextprotocol.io/docs/sdk.

## Register an MCP server with Windows

Once you've built an MCP server, you can register it with Windows in several ways. The method you choose depends on how your application is packaged and deployed.

### Apps with package identity

Applications with package identity can register with Windows by including required metadata in your app package. The OS will automatically register and unregister your server when the app package is installed and uninstalled. For more information, see [Register an MCP server from an app with package identity](mcp-windows-identity.md).

Apps are granted package identity when they are packaged using the MSIX package format. Unpackaged apps can be referenced in an MSIX package, granting them package identity, by using packaging with external location. For more information, see:

* [An overview of Package Identity in Windows apps](/windows/apps/desktop/modernize/package-identity-overview)
* [What is MSIX?](/windows/msix/overview)
* [Grant package identity by packaging with external location](/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps-overview)

### Apps without identity

If you have an server without package identity, such as a .exe file, a file packaged using MSI, or a standalone MCP bundle, and you don't want to use MSIX with external location to grant package identity, you can install an MCP bundle directly with your installer. Directly installed MCP bundles can't run in the securely contained agent process and will not be accessible from the Windows on-device agent registry unless users explicitly enable the option to Reduce protections for agent connectors in Windows Settings. For information on installing registering an MCP server distributed as an MCP bundle, see [Register an MCP server with an MCP bundle](mcp-mcpb.md).

### Manual registration

If you want to register a remote MCP servers or if you need fine-grained control when registering a local MCP server, you can manually register your server using the Windows on-device agent registry command-line tool. For more information, see [Manually register remote and local MCP servers](mcp-manual.md).

## MCP server containment

By default, MCP servers accessed through the Windows on-device agent registry (ODR) are run in an agent session, securely contained in a separate environment and can only allowed access to approved resources, limiting vulnerability to threats like cross-prompt injection attacks. For information about the restrictions and requirements of MCP server containment, see [Securely containing MCP servers on Windows](mcp-containment.md).

## Testing your MCP server on Windows

MCP on Windows provides a few different ways to test your MCP registration, to validate that it is being recognized by the ODR, and to test the functionality of your MCP server on Windows. For more information, see [Testing MCP servers on Windows](test-mcp-server.md).



