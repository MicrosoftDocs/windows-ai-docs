---
title: Learn how to register an MCP server from an app with package identity
description: TODO
ms.date: 11/10/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Register an MCP server from an app with package identity

This article describes how apps that have package identity can register an MCP server by declaring an app extension in the package manifest XML file. The OS will automatically register the server when the app package is installed. Apps that are packaged using the MSIX package format have package identity. Unpackaged apps can be granted package identity by building and registering a package with external location with your app. For more information, see [Grant package identity by packaging with external location](/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps-overview).

If you have an app that doesn't have package identity, such as one that is installed using MSI or just a standalone .exe, and you don't want to use MSIX packaging to grant it package identity, you can install an MCP server using an MCP Bundle. For more information, see [Register an MCP server with an MCP bundle](mcp-mcpb.md).


> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- Be on Windows build TODO-AddBuild or higher
- Ensure you have the latest [SignTool.exe](https://learn.microsoft.com/dotnet/framework/tools/signtool-exe), version 10.0.26100.4188 or greater. SignTool.exe ships with the [Windows SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/). You can get the latest Windows SDK using WinGet.
`winget install Microsoft.WindowsSDK.10.0.26100`
- For production releases, you will need a certificate that is part of the [Microsoft Trusted Root Program.](https://learn.microsoft.com/security/trusted-root/program-requirements)
- An MCP server. For more information, see [MCP server overview](./mcp-server-overview.md)
- An app with package identity and an AppxManifest.xml file
- NodeJS installed. To install NodeJS using WinGet, use the following command:
	- `winget install OpenJS.NodeJS`

## Overview

Adding an MCP server to an app with package identity includes the following steps:

- Add an MCP Bundle `manifest.json` file that describes your MCP server to your project. For more information on the format of this file, see the MCP bundle github repo, [github.com/anthropics/mcpb/](https://github.com/anthropics/mcpb/).
- Add an [uap3:AppExtension](/uwp/schemas/appxpackage/uapmanifestschema/element-uap3-appextension-manual) entry to your `AppxManifest.xml` file.

The remaining steps in this walkthrough will use samples from the MCP on Windows samples repo, [github.com/microsoft/mcp-on-windows-samples](https://github.com/microsoft/mcp-on-windows-samples)

## Clone the sample

Clone the MCP on Windows samples repo.

```powershell
git clone https://github.com/microsoft/mcp-on-windows-samples.git
```

Navigate to the MSIX app with server sample. 

```powershell
cd .\mcp-on-windows-samples\msix-app-with-server\
```


## Set up and build the sample

Open the `.sln` file from the sample in Visual Studio, select the correct architecture for your device from the **Solution Platforms** drop-down, and then build the solution.

Run the sample to launch the app's UI, and then run the MCP server by opening a PowerShell prompt to that folder and running this command:

```powershell
npx @modelcontextprotocol/inspector .\McpServer\bin\x64\Debug\net8.0\McpServer.exe
```

Note, you may need to change the path based on your architecture. This will spawn the MCP inspector and will allow you to run sample commands to your MCP server and see the effect in the running app.

## Add a `manifest.json` file that describes your MCP server

The MCP bundle config JSON file describes your server and how to interact with it. For more information on the MCP bundle config file format describing the required and optional fields, see (TODO-Add-link-to-MSFT-reference). 

1. In **Solution Explorer**, right-click the `Assets` folder under the `mcp-server-msix` project and select **Add->New Item...**
1. 

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

## Request capabilities for your server

Since your MCP server is running in a [contained environment](./mcp-containment.md), you can declare the [capabilities](https://learn.microsoft.com/en-us/windows/uwp/packaging/app-capability-declarations) it needs to access resources from the host. 

### Access to user's folders

By default, the contained workspace does not have access to user files. To enable file access on behalf of an agent, specify the appropriate known folder capabilities.
For this initial release, Windows restricts access to a limited set of user's folders. Users must explicitly consent to share these files with the MCP host before your server can access them.
* documentsLibrary – Grants access to the user’s Documents folder.
* downloadsFolder – Grants access to the user’s Downloads folder.
* picturesLibrary – Grants access to the user’s Pictures folder.
* musicLibrary – Grants access to the user’s Music folder.
* videosLibrary – Grants access to the user’s Videos folder.

```xml
  <Applications>
    <Application Id="App"
                 Executable="YourApp.exe"
                 EntryPoint="Windows.FullTrustApplication">
      <uap:VisualElements
        DisplayName="Your App"
        Description="Sample app accessing user libraries"
        Square150x150Logo="Assets\Logo.png"
        Square44x44Logo="Assets\SmallLogo.png"
        BackgroundColor="transparent"/>

      <!-- REQUIRED when using documentsLibrary:
           Declare file types you intend to access in the Documents library -->
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap:FileTypeAssociation Name="docs-types">
            <uap:SupportedFileTypes>
              <!-- List all file types you will open/read in Documents -->
              <uap:FileType>.txt</uap:FileType>
              <uap:FileType>.docx</uap:FileType>
              <uap:FileType>.pdf</uap:FileType>
              <!-- add more as needed -->
            </uap:SupportedFileTypes>
            <uap:DisplayName>Documents Types</uap:DisplayName>
            <uap:Logo>Assets\FileIcon.png</uap:Logo>
          </uap:FileTypeAssociation>
        </uap:Extension>
      </Extensions>

    </Application>
  </Applications>

  <Capabilities>
    <!-- Standard UAP capabilities -->
    <uap:Capabilities>
      <!-- User libraries -->
      <uap:Capability Name="picturesLibrary" />
      <uap:Capability Name="musicLibrary" />
      <uap:Capability Name="videosLibrary" />

      <!-- Downloads folder (Windows 10, version 2004+) -->
      <uap:Capability Name="downloadsFolder" />

      <!-- Restricted capability. Must pair with the FileTypeAssociation above. -->
      <uap:Capability Name="documentsLibrary" />
    </uap:Capabilities>
  </Capabilities>
</Package
```

## Test your MCP server

Now the `McpServer.exe` binary that is included in the sample project is automatically registered in the Windows MCP registry.

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 
