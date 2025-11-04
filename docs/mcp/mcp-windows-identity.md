---
title: Register an MCP server with Windows identity
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server from an MSIX app

If you're writing an MSIX app, you can include your MCP server's manifest so that when the MSIX package is installed, Windows will automatically register the MCP server within.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-AddBuild or higher
- Ensure you have the latest [SignTool.exe](https://learn.microsoft.com/en-us/dotnet/framework/tools/signtool-exe), version 10.0.26100.4188 or greater. SignTool.exe ships with the [Windows SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/). You can get the latest Windows SDK using WinGet.
`winget install Microsoft.WindowsSDK.10.0.26100`
- For production releases, you will need a certificate that is part of the [Microsoft Trusted Root Program.](https://learn.microsoft.com/en-us/security/trusted-root/program-requirements)
- An MCP server as part of your Windows app
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./quickstart-mcp-client.md)
- A packaged app and an AppxManifest.xml file
    - You can either use an MSIX application (TODO: Add link to MSIX docs on how to set one up) or you can [grant identity to nonpackaged apps]([url](https://learn.microsoft.com/en-us/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps).
- NodeJS installed
	- `winget install OpenJS.NodeJS`

## Overview

To add an MCP server to your app, you will need to only do these steps:

- Add a [MCP Bundle](https://github.com/anthropics/mcpb/) `manifest.json` file that describes your MCP server.
- Add an AppExtension entry to your `AppxManifest.xml` 

Let's look at how to do that using a sample. 

## Clone the sample

Clone the [MCP on Windows MSIX server sample](https://github.com/microsoft/mcp-on-windows-samples/tree/main/mcp-server-msix) to your device and navigate to it:

```powershell
https://github.com/microsoft/mcp-on-windows-samples.git; cd mcp-on-windows-samples/mcp-server-msix
```

This quickstart will walk you through the sample and its key concepts. 

## Set up and build the sample

Open the `.sln` file from the sample in Visual Studio and build the solution. 

Run the sample to open up its UI, and then run the MCP server by opening a PowerShell prompt to that folder and running this command:

```powershell
npx @modelcontextprotocol/inspector .\McpServer\bin\x64\Debug\net8.0\McpServer.exe
```

Note, you may need to change the path based on your architecture. This will spawn the MCP inspector and will allow you to run sample commands to your MCP server and see the effect in the running app. 

Now let's examine exactly how we can make this MCP server automatically be registered on Windows when this app is installed. 

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

You can find the [sample manifest.json here](https://github.com/microsoft/mcp-on-windows-samples/blob/main/mcp-server-msix/mcp-server-msix/Assets/manifest.json).

## Add an extension entry to `AppxManifest.xml`

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

This addition [can be found here in the sample](https://github.com/microsoft/mcp-on-windows-samples/blob/cfb1563104efc47668fc895a45e0c0c07838a7e1/mcp-server-msix/mcp-server-msix/Package.appxmanifest#L50).

## Test your MCP server

Now the `McpServer.exe` binary that is included in the sample project is automatically registered in the Windows MCP registry.

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 
