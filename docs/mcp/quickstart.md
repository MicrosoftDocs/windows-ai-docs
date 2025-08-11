---
title: MCP for Windows quick start guide
description: Get started quickly with Model Context Protocol (MCP) on Windows. Learn how to set up your first MCP integration in minutes.
ms.date: 08/11/2025
ms.topic: quickstart
keywords: MCP, Model Context Protocol, Windows, quickstart, tutorial
no-loc: [Model Context Protocol, MCP, Windows AI Foundry]
---

# Quickstart: MCP for Windows

This quickstart will help you get up and running with Model Context Protocol (MCP) on Windows in just a few minutes. You'll learn how to set up a basic MCP integration that connects an AI application to local file system data.

## Prerequisites

Before you begin, ensure you have:

- Windows 10 version 1903 or later, or Windows 11
- Visual Studio 2022 17.8 or later with the **.NET desktop development** workload
- .NET 8.0 or later
- An AI application that supports MCP (such as Claude Desktop, AI Dev Gallery, or a custom application)

## What you'll build

In this quickstart, you'll create:

1. A simple MCP server that provides access to local documents
2. Configuration to connect your AI application to the MCP server
3. A test scenario to verify the integration works

## Step 1: Set up your development environment

First, create a new directory for your MCP server:

1. Open a command prompt or PowerShell window
2. Create and navigate to a new directory:

   ```powershell
   mkdir MyMCPServer
   cd MyMCPServer
   ```

3. Initialize a new .NET console application:

   ```powershell
   dotnet new console
   dotnet add package Microsoft.Extensions.Hosting
   dotnet add package Microsoft.Extensions.Logging
   dotnet add package System.Text.Json
   ```

## Step 2: Create the MCP server

Replace the contents of `Program.cs` with the following code:

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using System.Text.Json;

var builder = Host.CreateDefaultBuilder(args);

builder.ConfigureServices(services =>
{
    services.AddSingleton<McpServer>();
});

var host = builder.Build();

var mcpServer = host.Services.GetRequiredService<McpServer>();
await mcpServer.RunAsync();

public class McpServer
{
    private readonly ILogger<McpServer> _logger;

    public McpServer(ILogger<McpServer> logger)
    {
        _logger = logger;
    }

    public async Task RunAsync()
    {
        _logger.LogInformation("MCP Server starting...");
        
        // Read from stdin and write to stdout for JSON-RPC communication
        using var reader = new StreamReader(Console.OpenStandardInput());
        using var writer = new StreamWriter(Console.OpenStandardOutput()) { AutoFlush = true };

        await ProcessMessages(reader, writer);
    }

    private async Task ProcessMessages(StreamReader reader, StreamWriter writer)
    {
        string? line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            try
            {
                var request = JsonSerializer.Deserialize<JsonDocument>(line);
                var response = await HandleRequest(request);
                
                if (response != null)
                {
                    await writer.WriteLineAsync(JsonSerializer.Serialize(response));
                }
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error processing message: {Message}", line);
            }
        }
    }

    private async Task<object?> HandleRequest(JsonDocument request)
    {
        if (!request.RootElement.TryGetProperty("method", out var methodElement))
            return null;

        var method = methodElement.GetString();
        var id = request.RootElement.TryGetProperty("id", out var idElement) ? idElement : (JsonElement?)null;

        return method switch
        {
            "initialize" => HandleInitialize(id),
            "resources/list" => await HandleResourcesList(id),
            "resources/read" => await HandleResourcesRead(request, id),
            _ => CreateErrorResponse(id, -32601, "Method not found")
        };
    }

    private object HandleInitialize(JsonElement? id)
    {
        return new
        {
            jsonrpc = "2.0",
            id = id?.GetInt32(),
            result = new
            {
                protocolVersion = "2024-11-05",
                capabilities = new
                {
                    resources = new { }
                },
                serverInfo = new
                {
                    name = "Windows Documents MCP Server",
                    version = "1.0.0"
                }
            }
        };
    }

    private async Task<object> HandleResourcesList(JsonElement? id)
    {
        var documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
        var files = Directory.GetFiles(documentsPath, "*.txt", SearchOption.TopDirectoryOnly)
            .Take(10) // Limit to first 10 files for this example
            .Select(file => new
            {
                uri = $"file:///{file.Replace('\\', '/')}",
                name = Path.GetFileName(file),
                description = $"Text file: {Path.GetFileName(file)}",
                mimeType = "text/plain"
            })
            .ToArray();

        return new
        {
            jsonrpc = "2.0",
            id = id?.GetInt32(),
            result = new
            {
                resources = files
            }
        };
    }

    private async Task<object> HandleResourcesRead(JsonDocument request, JsonElement? id)
    {
        if (!request.RootElement.TryGetProperty("params", out var paramsElement) ||
            !paramsElement.TryGetProperty("uri", out var uriElement))
        {
            return CreateErrorResponse(id, -32602, "Invalid params");
        }

        var uri = uriElement.GetString();
        if (uri == null || !uri.StartsWith("file:///"))
        {
            return CreateErrorResponse(id, -32602, "Invalid file URI");
        }

        var filePath = uri.Substring(8).Replace('/', '\\'); // Convert back to Windows path
        
        try
        {
            if (!File.Exists(filePath))
            {
                return CreateErrorResponse(id, -32603, "File not found");
            }

            var content = await File.ReadAllTextAsync(filePath);
            
            return new
            {
                jsonrpc = "2.0",
                id = id?.GetInt32(),
                result = new
                {
                    contents = new[]
                    {
                        new
                        {
                            uri = uri,
                            mimeType = "text/plain",
                            text = content
                        }
                    }
                }
            };
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error reading file: {FilePath}", filePath);
            return CreateErrorResponse(id, -32603, "Internal error");
        }
    }

    private object CreateErrorResponse(JsonElement? id, int code, string message)
    {
        return new
        {
            jsonrpc = "2.0",
            id = id?.GetInt32(),
            error = new
            {
                code,
                message
            }
        };
    }
}
```

## Step 3: Test the MCP server

Build and test your MCP server:

1. Build the application:

   ```powershell
   dotnet build
   ```

2. Run the server (it will wait for input):

   ```powershell
   dotnet run
   ```

3. In another command prompt window, test the server by sending a JSON-RPC message:

   ```powershell
   echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{}}' | dotnet run --project .
   ```

You should see a response indicating the server initialized successfully.

## Step 4: Configure your AI application

Now you need to configure your AI application to use the MCP server. The exact steps depend on your AI application:

### For Claude Desktop

Create a configuration file at `%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "windows-documents": {
      "command": "dotnet",
      "args": ["run", "--project", "C:\\path\\to\\your\\MyMCPServer"],
      "env": {}
    }
  }
}
```

### For Custom Applications

Use the MCP client libraries appropriate for your application's programming language to connect to your server.

## Step 5: Test the integration

1. Create a few text files in your Documents folder for testing
2. Start your AI application
3. Ask the AI to list available documents or read a specific document
4. Verify that the AI can access and read your local files through MCP

Example prompts to try:

- "What documents do you have access to?"
- "Please read the contents of [filename].txt"
- "Summarize all the text files in my Documents folder"

## What you've accomplished

You've successfully:

✅ Created a basic MCP server that provides access to local documents
✅ Implemented the core MCP protocol methods (initialize, resources/list, resources/read)
✅ Connected an AI application to your local data through MCP
✅ Tested the integration with real file access

## Next steps

Now that you have a working MCP integration, you can:

- **Expand data sources**: Add support for databases, APIs, or other Windows applications
- **Implement security**: Add authentication and authorization to control access
- **Add more capabilities**: Implement tools, prompts, and sampling features
- **Learn about architecture**: Understand MCP architecture patterns and best practices
- **Review security**: Read about identity and security considerations for production deployments

## Troubleshooting

### Server doesn't start

- Verify .NET 8.0 or later is installed: `dotnet --version`
- Check that all NuGet packages are restored: `dotnet restore`

### AI application can't connect

- Verify the path in your configuration file is correct
- Check that the server executable has proper permissions
- Review the AI application's logs for connection errors

### No files appear in resource list

- Ensure you have .txt files in your Documents folder
- Check file permissions and make sure the files are readable
- Review the server logs for any error messages

## See also

- [MCP for Windows overview](overview.md)
- [Windows AI Foundry overview](../overview.md)
