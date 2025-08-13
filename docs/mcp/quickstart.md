---
title: MCP for Windows quick start guide
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# MCP on Windows quickstart

This guide focuses on integrating an MCP server into your Windows app. Please see the [MCP client quickstart guide](./quickstart-mcp-client.md) to learn how to allow your application to connect to different MCP servers on Windows.

## Step 1: Author your MCP server

First you need to build the MCP server in your app. 

This doc page focuses on integrating the MCP server into your Windows app, and so building the MCP server is out of scope. Please view the [Model Context Protocol Docs for SDKs and getting started for MCP servers](https://modelcontextprotocol.io/docs/sdk). 

When this step is completed your application should have a binary inside of it that is an MCP server.

## Step 2: Register your MCP server with Windows


### Option 1 - You have a packaged app (MSIX)

Edit your app manifest to declare an AppExtension and optional execution alias.

TODO flesh this out.

```xml
<Extensions>
	<uap3:Extension Category="windows.appExtension">
		<uap3:AppExtension
			Name="com.microsoft.windows.ai.mcpServer"
			Id="WindowsMCPServerSampleApp"
			DisplayName="Windows Sample MCP Servers"
			PublicFolder="Assets">
			<uap3:Properties>
				<Registration>mcpServerConfig.json</Registration>
			</uap3:Properties>
		</uap3:AppExtension>
	</uap3:Extension>

	<uap5:Extension Category="windows.appExecutionAlias">
		<uap5:AppExecutionAlias>
			<uap5:ExecutionAlias Alias="WindowsMCPServerSampleApp.exe"/>
		</uap5:AppExecutionAlias>
	</uap5:Extension>
</Extensions>
```

### Option 2 - You have an unpackaged app (.exe, MSI, etc.)

You can either grant your app identity, or proceed without doing so.

#### Using sparse packaging to grant your app identity

If you ship an unpackaged app, add identity using a sparse package and declare the same AppExtension and registration file. At install/update time, register the sparse package so Windows can discover your MCP server.

TODO: Provide a minimal sparse package manifest and registration steps

#### Proceed without identity

TODO. Not sure on the steps required here.

## Troubleshooting

TODO

## Next steps

TODO
