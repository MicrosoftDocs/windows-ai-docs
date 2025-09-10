---
title: Register an MCP server with Windows identity
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft with initial approval

# Register an MCP server from an MSIX app

If you're writing an MSIX app, you can include your MCP server's manifest so that when the MSIX package is installed, Windows will automatically register the MCP server within.

## Pre-requisites

- Be on Windows build TODO-AddBuild or higher 
- An MCP server as part of your Windows app
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./build-mcp-server.md)
- A packaged app and an AppManifest.xml file
    - You can either use an MSIX application (TODO: Add link to MSIX docs on how to set one up) or you can [grant identity to nonpackaged apps]([url](https://learn.microsoft.com/en-us/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps).

To add an MCP server to your app, you will need to only do two things:

- Add a [MCP Bundle](https://github.com/anthropics/mcpb/) `manifest.json` file that describes your MCP server.
- Add an AppExtension entry to your `AppManifest.xml` 

## Add a `manifest.json` file that describes your MCP server

The MCP bundle config JSON file describes your server and how to interact with it. It follows the MCP bundle manifest reference (TODO-Add-link-to-MSFT-reference) which can give you more info on the required and optional fields. 

Below is a sample JSON you can adapt to your project:

```json
{
  "manifest_version": "0.1", // Manifest spec version this manifest conforms to
  "name": "my-extension", // Machine-readable name (used for CLI, APIs)
   "author": {
    "name": "My Name" // Author's name (required field)
  },
  "version": "1.0.0", // Semantic version of your extension
  "description": "A simple MCP extension", 
  "server": {
    "type": "binary", // Server type: "node", "python", or "binary"
    "entry_point": "myMCPServer.exe", // Path to the main server file

  // TODO - Containment / other security needs
  }
}
```

## Add an extension entry to `AppManifest.xml`

Adding the MCP extension entry allows the app identity platform to handle the MCP registration and unregistration for you.
Below is a sample extension you can adapt to your app:

```xml
<Extensions>
	<uap3:Extension Category="windows.appExtension">
		<uap3:AppExtension
			Name="com.microsoft.windows.ai.mcpServer"
			Id="WindowsMCPServerSampleApp"
			DisplayName="Windows Sample MCP Server"
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

## Test your MCP server

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 

If all tests look good, then you are finished!

## Learn more

- Visit the app actions page (TODO: Add this link) to learn about how any app action can become an MCP server
