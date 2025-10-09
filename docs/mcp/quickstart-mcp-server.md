---
title: MCP server on Windows quick start guide
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft with initial approval

# MCP on Windows quickstart

This guide focuses on integrating an MCP server into your Windows app. Please see the [MCP client quickstart guide](./quickstart-mcp-client.md) to learn how to allow your application to connect to different MCP servers on Windows.

## Pre-requisites

- Be on Windows build TODO-Add-this-number or higher

## Step 1: Author your MCP server

First you need to build the MCP server in your app. [The MCP server build guide can be found here](./build-mcp-server.md).

When this step is completed your application project should include a console exe that implements MCP over stdio, and now you will just need to register it with Windows so your end user's AI agents and MCP clients can automatically use it.

## Step 2: Register your MCP server with Windows

There are a few different options on how you can register your MCP server with Windows, depending on how your application is deployed  and whether it has identity. This guide will help you choose an option:

- Do you have an MSIX app? 
   - [Fast track your development with MSIX's built in MCP integration](./mcp-windows-identity.md)
- Do you have an app without identity (MSI, exe, etc.) or an existing MCP bundle?
   - [Add Windows identity to your application and use its identity to minimize dependency requirements](./mcp-windows-identity.md#pre-requisites)
   - [OR Package your MCP server within an MCP bundle and include it in your app](./mcp-mcpb.md)
- Do you want to run registration methods manually (e.g. you are using a different install technology, or want to dynamically decide whether to make your MCP server available):
   - [Use the registration methods manually, useful for developing installers or low level operations](./mcp-manual.md)
