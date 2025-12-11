---
title: Learn how to register an MCP server from an app with package identity
description: Register an MCP server from an app with package identity
ms.date: 11/10/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server from an app with package identity

This article describes how an app with package identity can register an MCP server by walking through a sample from the MCP on Windows samples repo, [github.com/microsoft/mcp-on-windows-samples](https://github.com/microsoft/mcp-on-windows-samples), to illustrate how an app with package identity registers an MCP server. When you add the required metadata to your packaged app, the OS will automatically register the server when the app package is installed. Apps that use the MSIX package format have package identity. Unpackaged apps can be granted package identity by building and registering a package with external location with your app. For more information, see [Grant package identity by packaging with external location](/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps-overview).

If you have an app that doesn't have package identity, such as one that is installed using MSI or just a standalone .exe, and you don't want to use MSIX packaging to grant it package identity, you can install an MCP server using an MCP Bundle. For more information, see [Register an MCP server with an MCP bundle](mcp-mcpb.md).


> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Windows build 26220.7262 or higher
- Ensure you have the latest [SignTool.exe](/dotnet/framework/tools/signtool-exe), version 10.0.26100.4188 or greater. SignTool.exe ships with the [Windows SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/). You can get the latest Windows SDK using WinGet.
`winget install Microsoft.WindowsSDK.10.0.26100`
- For production releases, you will need a certificate that is part of the [Microsoft Trusted Root Program.](/security/trusted-root/program-requirements)
- An MCP server. For more information, see [MCP server overview](./mcp-server-overview.md)
- An app with package identity and an AppxManifest.xml file
- NodeJS installed. To install NodeJS using WinGet, use the following command:
	- `winget install OpenJS.NodeJS`

## Overview

Adding an MCP server to an app with package identity includes the following steps:

- Add an MCP Bundle `manifest.json` file that describes your MCP server to your project. For more information on the format of this file, see the MCP bundle github repo, [github.com/anthropics/mcpb/](https://github.com/anthropics/mcpb/).
- Add an [uap3:AppExtension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) entry to your `AppxManifest.xml` file.

## Clone the sample

Clone the MCP on Windows samples repo.

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
```

The solution file for the MSIX app that registers the MCP server is in the `mcp-on-windows-samples\msix-app-with-server\` directory.

## Set up and build the sample

Open the `.sln` file from the sample in Visual Studio, select the correct architecture for your device from the **Solution Platforms** drop-down.

The app requires a certificate to build successfully. To add a test certificate:

1. In **Solution Explorer**, under the `mcp-server-msix` project, double-click the file `Package.appxmanifest` to open the package manifest editor.
1. On the **Packaging** tab, click **Choose Certificate**.
1. In the **Choose a Certificate** dialog, click **Create...**
1. Leave the default values in all of the fields and click **OK**. Click **OK** again to complete the certificate creation process.

Build the sample. In **Solution Explorer**, right-click the `mcp-server-msix` project and select **Deploy**. Now run the app.

Next, launch the MCP server by opening a PowerShell prompt to that folder and running the following command. You may need to change the path based on your machine's architecture.

```powershell
npx @modelcontextprotocol/inspector .\McpServer\bin\x64\Debug\net8.0\McpServer.exe
```

This command will start the MCP server. It will also launch the MCP inspector, a utility for interacting with MCP servers, in a browser Window. Switch back to the sample app to use the app's UI to interact with the MCP server.

The rest of this walkthrough will call out the components of this sample app that cause it to be registered upon installation.

## The MCP bundle (MCPB) config JSON file

The MCP bundle (MCPB) config JSON file describes your server and how to interact with it. The MCP bundle config JSON file for the example project is found in the file `Assets/manifest.json`. The code listing is shown below. Note that it provides metadata, such as a name, description, and author for the server. Next it defines three different tools and defines the expected input types. For a detailed description of the MCPB config file format, see [MCPB Manifest.json Spec](https://github.com/modelcontextprotocol/mcpb/blob/main/MANIFEST.md) on the [MCPB github repo](https://github.com/modelcontextprotocol/mcpb/).

Note that the values in the **_meta** section of the MCPB config file must match what your server returns at runtime in order for your server to run in default mode. For more information, see [The _meta section of the MCPB config JSON file](mcp-mcpb.md#the-_meta-section-of-the-mcpb-config-json-file).


```json
{
  "manifest_version": "0.1",
  "name": "MCP-Server-Sample-App",
  "version": "1.0.0",
  "description": "A Sample App MCP Server",
  "author": {
    "name": "Microsoft"
  },
  "server": {
    "type": "binary",
    "entry_point": "SampleMCPServer.McpServer.exe",
    "mcp_config": {
      "command": "SampleMCPServer.McpServer.exe",
      "args": []
    }
  },
  "tools": [
    {
      "name": "get_random_fact",
      "description": "Gets a random interesting fact from an online API."
    },
    {
      "name": "get_random_quote",
      "description": "Gets a random inspirational quote from an online API."
    },
    {
      "name": "get_weather",
      "description": "Gets current weather information for a specified city."
    }
  ],
  "tools_generated": false,
  "license": "MIT",
  "_meta": {
    "com.microsoft.windows": {
      "static_responses": {
        "initialize": {
          "protocolVersion": "2025-06-18",
          "capabilities": {
            "logging": {},
            "tools": {
              "listChanged": true
            }
          },
          "serverInfo": {
            "name": "McpServer",
            "version": "1.0.0.0"
          }
        },
        "tools/list": {
          "tools": [
            {
              "name": "get_random_quote",
              "description": "Gets a random inspirational quote from an online API.",
              "inputSchema": {
                "type": "object",
                "properties": {}
              }
            },
            {
              "name": "get_random_fact",
              "description": "Gets a random interesting fact from an online API.",
              "inputSchema": {
                "type": "object",
                "properties": {}
              }
            },
            {
              "name": "get_weather",
              "description": "Gets current weather information for a specified city.",
              "inputSchema": {
                "type": "object",
                "properties": {
                  "city": {
                    "type": "string",
                    "default": "London"
                  }
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

## The MCP app extension entry in the package manifest file

You register your MCP server with the system by adding a [uap3:AppExtension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) element to your app's package manifest file,  `AppxManifest.xml`. The name of the extension is `com.microsoft.windows.ai.mcpServer`. This extension allows Windows to register and unregister your MCP server when the app is installed or uninstalled. The [uap5:AppExecutionAlias](/uwp/schemas/appxpackage/uapmanifestschema/element-uap5-appexecutionalias) tells the system the executable for your MCP server.

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

You can view the code of the sample project by right-clicking the `Package.appxmanifest` file and selecting **View Code**.

## Request capabilities for your server

An MCP server registered with a package app will run in a contained environment, which means that by default it won't have access to protected resources like the files of the current user. For more information, see [Securely containing MCP servers on Windows](mcp-containment.md).

You can request access to sets of resources by declaring capabilities in your app manifest file. When your server is accessed, the system will prompt the user allow access to the requested resources. Most capabilities can be set directly in the manifest editor UI. Some common resources that can be accessed via capabilities include:

* documentsLibrary – Grants access to the user’s Documents folder.
* downloadsFolder – Grants access to the user’s Downloads folder.
* picturesLibrary – Grants access to the user’s Pictures folder.
* musicLibrary – Grants access to the user’s Music folder.
* videosLibrary – Grants access to the user’s Videos folder.

For more information about capabilities, see [App Capability Declarations](/windows/uwp/packaging/app-capability-declarations).

## Test your MCP server

For information on testing the registration and functionality of an MCP server, see [Testing MCP servers on Windows](./test-mcp-server.md). 
