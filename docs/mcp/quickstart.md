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

# NOTE THESE DOCS ARE CURRENTLY WIP.IT WILL BE FINALIZED AFTER THE SPEC DESIGN IS COMPLETE.

## Pre-requisites

- Ensure you are on Windows build TODO-Addthisversion or higher
- [Developer mode on Windows is enabled](https://learn.microsoft.com/windows/apps/get-started/enable-your-device-for-development#activate-developer-mode)

## Step 1: Author your MCP server

First you need to build the MCP server in your app. 

This doc page focuses on integrating the MCP server into your Windows app, and so building the MCP server is out of scope. Please view the [Model Context Protocol Docs for SDKs and getting started for MCP servers](https://modelcontextprotocol.io/docs/sdk). 

When this step is completed your application should have an MCP server binary inside of it.

## Step 2: Register your MCP server with Windows

There are a few different options on how you can register your MCP server with Windows, depending on how your application is packaged and has identity. 

### Option 1 - You have a packaged app (MSIX)

Edit your app manifest to declare an AppExtension and optional execution alias.

TODO flesh this out. Good enough for now. 

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

Please complete the steps to [grant identity to non packaged apps](https://learn.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps) first.

Once completed, you should now have a `AppxManifest.xml` that describes your overall application, and [manifests for the different executables](https://learn.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps#add-identity-metadata-to-your-desktop-application-manifests) in your app. 

Now you just need to add the MCP extensions content to your `AppxManifest.xml` to register your MCP server.

```xml
<Extensions>
	<uap3:Extension Category="windows.appExtension">
		<uap3:AppExtension
		Name="com.microsoft.windows.ai.mcpServer"
		Id="ContosoMCPServer"
		DisplayName="Contoso MCP Server"
		PublicFolder="Assets">
		<uap3:Properties>
			<Registration>mcpServerConfig.json</Registration>
		</uap3:Properties>
		</uap3:AppExtension>
	</uap3:Extension>
</Extensions>
```

This AppExtension points to an `mcpServerConfig.json` file which specifies the command executable associated with the MCP server. 

The contents of `mcpServerConfig.json` are:

```json
{
  "version": 1,
  "mcpServers": [
    {
      "name": "ContosoMCPServer",
      "type": "stdio",
      "command": "ContosoMCPServer.exe"
    }
  ]
}
```

The executable referenced here (`ContosoMCPServer.exe`) is a console application, that when run for the first time without identity registers the sparse package and restarts itself. 

TODO: Why is that the case?? How can it get run the first time if it doesn't have identity to register itself?????

TODO: Fill out the rest of what the application needs to do, and a sample of that C# app

#### Proceed without identity

TODO. Not sure on the steps required here.

## Troubleshooting

TODO

## Next steps

- TODO - testing the MCP server
- TODO - Integrating an MCP client
- TODO - Remote MCP servers and other kinds