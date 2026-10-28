---
title: Test MCP servers on Windows
description: Test MCP server functionality, Windows registration, and contained agent-host integration with MCP Inspector and the on-device registry.
ms.date: 10/28/2026
ms.topic: concept-article
author: GrantMeStrength
ms.author: jken
keywords: MCP, Model Context Protocol, Windows, architecture, design, implementation
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Test MCP servers on Windows

Test a Model Context Protocol (MCP) server at three levels: its protocol behavior, its Windows registration, and its behavior through the intended agent host. Passing one level doesn't establish that the others work.

> [!NOTE]
> **Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

## Prerequisites

- A development MCP server and test data that you can safely read or modify. For a local server, know the command, arguments, and environment needed to start it. For a remote server, know its endpoint, transport, and authentication requirements.
- A supported [Node.js](https://nodejs.org/en/download) installation. The current [MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector) requires Node.js 22.19.0 or later. Check its requirements when selecting the Inspector version for your tests.
- For Windows registration and contained execution, Windows 11 build 26220.7262 or later with the MCP feature available and enabled. Follow the current [package-identity registration prerequisites](mcp-windows-identity.md#prerequisites), including signing requirements, for your deployment.
- An agent host that supports discovery and connection through the Windows on-device agent registry (ODR), if you're testing that integration.

Check your Node.js version in PowerShell:

```powershell
node --version
```

Direct Inspector testing doesn't require Windows registration. A local binary server needs package identity and the required registration metadata to run contained through Windows. See [Securely containing MCP servers on Windows](mcp-containment.md) for the applicable requirements.

## Test protocol behavior with MCP Inspector

MCP Inspector can connect directly to a server so you can examine its initialization, tools, results, and errors before testing Windows integration. Only launch executables and connect to endpoints that you trust.

### Test a local stdio server

Replace the example path with your server executable, then run:

```powershell
$serverPath = 'C:\path\to\YourMcpServer.exe'
npx @modelcontextprotocol/inspector "$serverPath"
```

Inspector starts the server and opens its web interface. Supply any arguments or environment variables that your server needs. For servers implemented in another runtime, pass the command that normally starts the server instead.

Check that the server:

- Initializes successfully and reports the expected name, version, and capabilities.
- Lists the expected tools with accurate descriptions and input and output schemas.
- Returns the expected results for valid input.
- Returns actionable errors for invalid input, unauthorized requests, and unavailable dependencies without exposing credentials or sensitive data.

For stdio servers, keep protocol messages on standard output and write diagnostic logs to standard error. Other output on standard output can interrupt the MCP connection.

For a command-line tool-list check, use Inspector's CLI client:

```powershell
npx @modelcontextprotocol/inspector --cli "$serverPath" --method tools/list
```

This checks a direct connection to the executable. It doesn't test Windows discovery or launch the server in a contained agent session. For repeatable automation, use an approved Inspector version and follow the [Inspector CLI guidance](https://modelcontextprotocol.io/docs/tools/inspector/cli), including authentication handling for non-interactive runs.

### Test a remote HTTP server

Start Inspector with your development server's endpoint and supported transport. For a Streamable HTTP endpoint, replace the example URL and run:

```powershell
$serverUrl = 'https://your-server.example/mcp'
npx @modelcontextprotocol/inspector --server-url "$serverUrl" --transport http
```

Complete the server's authentication flow and test its tools with permitted and restricted accounts. Don't place access tokens or client secrets in examples, screenshots, or shared logs.

A remote server runs on its own infrastructure. Connecting to it from Windows doesn't put that remote process inside a Windows agent session.

## Verify Windows registration

Install or register the server by following the procedure for its deployment model:

- [Register an MCP server from an app with package identity](mcp-windows-identity.md).
- [Register an MCP server with an MCP bundle](mcp-mcpb.md).
- [Manually register remote and local MCP servers](mcp-manual.md).

Use the MCP command group documented in the [ODR command-line reference](../odr-tool.md) to list registered servers:

```powershell
odr.exe mcp list
```

Check that the expected server and registration metadata appear. A listing verifies discovery metadata; it doesn't prove that the server can start, authenticate, or execute a tool.

Preview command surfaces can differ by Windows build. Inspect the help for your installed version if a command isn't recognized:

```powershell
odr.exe --help
odr.exe mcp --help
```

For default-mode Windows registration, compare the server's runtime `initialize` and `tools/list` responses with the manifest's static responses. Names, versions, capabilities, tool descriptions, and schemas must meet the matching requirements in [Register an MCP server with an MCP bundle](mcp-mcpb.md#the-_meta-section-of-the-mcpb-config-json-file). Repeat this check when updating either the implementation or manifest.

## Test through the intended agent host

Use the host and Windows registration path that your users will use. Don't substitute a direct launch of the executable for a test of ODR-mediated execution.

For a local package-identity server, test under the default agent-connector protections:

1. Discover the installed server through the host's Windows integration and connect to it. Confirm that the host uses the registered connection rather than a separately configured executable.
2. List tools and call a harmless read-only operation with test data. Verify the result and any expected consent prompts.
3. Test required resource access both before and after the applicable consent. Confirm that the server doesn't depend on direct access to the signed-in user's credentials, settings, files, or app windows.
4. Test denied permissions, canceled consent, expired authentication, and unavailable services. Confirm that failures are handled without silently broadening access.
5. Update and uninstall the package. Verify that registration changes as expected and the host no longer offers or uses a removed registration.

> [!IMPORTANT]
> Directly installed MCP bundles don't run contained under the default protections. Don't reduce agent-connector protections to make a containment test pass. Review the [containment requirements and limitations](mcp-containment.md) before choosing a registration path.

File-access consent applies to the host, not individually to each server used by that host. Use a test setup with known consent state when checking denial behavior, and don't assume another server used by the same host is excluded from a granted resource.

Windows containment limits access to local resources. Your back-end services must still authenticate the caller and authorize each operation. For tools that change data, also test the application's approval, cancellation, and retry behavior with non-production records.

## Troubleshoot failures

| Symptom | What to check |
|---|---|
| `node` or `npx` isn't found | Verify the Node.js installation and PATH, then open a new PowerShell window. |
| Inspector can't connect to a local server | Check the executable path, required arguments and environment, startup errors, and whether standard output contains non-protocol text. |
| ODR reports that the MCP surface isn't enabled | Verify the supported Windows build, feature availability, and applicable organization settings before troubleshooting the server itself. |
| The server doesn't appear in ODR | Check the installation or registration procedure and the installed command's help. For bundles, check the default-protection limitations. |
| Direct Inspector calls work, but host calls fail | Check the registered launch path, manifest/runtime agreement, containment restrictions, consent state, and authentication in the agent session. |

## Related content

- [MCP servers on Windows overview](mcp-server-overview.md)
- [The Windows on-device agent registry command-line tool](../odr-tool.md)
- [Securely containing MCP servers on Windows](mcp-containment.md)
- [MCP Inspector documentation](https://modelcontextprotocol.io/docs/tools/inspector)
