---
title: Register an MCP server with an MCP bundle
description: TODO
ms.date: 08/12/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# DOC STATUS : First draft

# Register an MCP server with an MCP bundle

[MCP bundles](https://github.com/anthropics/mcpb/) are a package format that standardize deployment for MCP servers. You can include an MCPB into your existing Windows app, or you can distribute the MCPB on its own.

## Pre-requisites


- Be on Windows build TODO-AddBuild or higher 
- Enable developer mode
- An existing MCP server
    - See our [MCP development guidance page to learn more about this step TODO:AddLink](./build-mcp-server.md)
- Have NodeJS installed
    - You can install with winget by running: `winget install OpenJS.NodeJS`

### Interanal only and temporary pre-reqs

For development purposes:

- Set up a ge_current_directwinpd_uxip VM
- Install in your VM
    - Latest Node, Git for Windows and .NET runtime 9
- Enable velocity key 58763365 using stagingtool or ixptools Set-DeviceVelocityKey
- Git clone WMSS and open `src/components/wmss/wmss.sln`
    - `git clone https://microsoft@dev.azure.com/microsoft/OS.Developer/_git/asklar`
- Build and deploy `wmss.exe` to your VM
- On the VM, figure out where the EXE got deployed, it should be somewhere under `c:\users\<youruserid>\AppData\Local\DevelopmentFiles`
- Import `wmss.reg` TODO: Where does this come from?
- Update the Path value under `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Mcp` to the path of the WMSS.exe on your VM

#### Test with the MCP inspector

- In a folder run `git clone https://github.com/modelcontextprotocol/servers.git` to clone a server for testing
- Register the 'everything'  server with the Windows MCP Registry
- Create an mcp.json in the src\everything subdirectory of the servers repo
   - ```
    {
        "type": "stdio",
        "name": "everything",
        "command": "npx",
        "args": ["@modelcontextprotocol/server-everything", "stdio"]
    }
    ```
- Run `npm install -g @modelcontextprotocol/server-everything@latest`
- Run `WMSS.exe --register <Path to mcp.json created in above step>`
- In another folder `git clone https://github.com/asklar/inspector.get` to get the inspector
- `git checkout wmss-integration`
- `npm install`
- `npm run build` to build the inspector
- `cd client`
- `npx tsx watch .\bin\start.js` to start the inspector
- Access the server via localhost and then hit 'Connect' to view locally registered servers

## Install the MCP bundle tooling package

Open the command line and install the NPM package with this command:

```powershell
npm install -g @anthropic-ai/mcpb
```

TODO - Validate this command is correct.

## Build your MCP Bundle

You can initialize and build your bundle by running `mcpb init` and `mcpb pack`, please [see the MCP bundle docs](https://github.com/anthropics/mcpb?tab=readme-ov-file#for-bundle-developers) for more information on this step.


## Sign your package

Now that you have a `.mcpb` file as part of your app, sign your package with this command:

```powershell
mcpb sign <path-to-mcpb-file>
```

- TODO: How do they get the `.cer` file for this signing?

## Distribute your `.mcpb` file, or include it as part of your app install

Either you can stop here and distribute your `.mcpb` file directly, as is, to ship a standalone MCP server.

Or you can integrate it as part of your application by running the `.mcpb` bundle to install and uninstall at install or uninstall of your app.

- TODO: What are the exact commands? `.mcpb /quiet` and `.mcpb /uninstall`? 

- TODO: nuance between signed binary + Trusted Launch path, vs. untrusted path
 
## Test your MCP server

You can now test that your MCP server shows up correctly as part of regular app install by test installing your app and then using the [testing guide](./test-mcp-server.md) to interact with it. 

If all tests look good, then you are finished!
