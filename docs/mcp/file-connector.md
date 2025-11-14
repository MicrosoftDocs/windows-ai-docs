---
title: Windows File Explorer MCP connector
description: Learn about the Windows File Explorer MCP connector
ms.date: 10/31/2025
ms.topic: overview
no-loc: [Model Context Protocol, MCP]
---

# Windows File Explorer MCP connector

> [!NOTE]
> **Some information relates to pre-released product which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.**

The Windows File Explorer MCP connector provides a set of tools to access and modify files using a Model Context Protocol (MCP) server. This connector provides access to common user folders including Documents, Desktop, Downloads, Music, Videos and Pictures, as well as public folders on the device. A feature of MCP that makes it so powerful is that agents can dynamically discover the tools that an MCP server provides, as well as the input and output parameters for each tool. So there is no need to hard code calls to a particular tool into a connector app.

The File Explorer MPC connect tools and parameters are shown below to illustrate the kinds of tools an MCP can provide.

## Permissions

> [!IMPORTANT]
> File access requires explicit user permission. 
> Users and administrators may deny access to files.

## File tools

| **Tool Name** | **Input** | **Output** | **Notes** |
|-|-|-|-|
| **get_file_details** | `path` (string) | `extension` (string/null), `size` (integer), `creationTime`, `lastAccessTime`, `lastWriteTime` | Input is a single file path; output provides metadata only, no file content. Read-only operation. |
| **create_directory** | `path` (string), `directoryName` (string) | `result` (string) | Creates a new directory; destructive operation because it changes the file system. |
| **move_file** | `oldFullPath` (string), `newFullPath` (string) | `result` (string) | Moves or renames a file; destructive because it alters file location. |
| **create_text_file** | `path` (string), `filename` (string), `content` (string) | `result` (string) | Creates a text file with optional content; destructive operation. |
| **read_file** | `filePath` (string) | `contents` (array), `_meta` (object/null) | Reads file content as a resource; read-only operation. |
| **get_directories** | `path` (string) | `result` (array of objects with `path`) | Lists directories within a given path; read-only operation. |
| **unzip_folder** | `zipPath` (string), `extractPath` (string) | `path` (string/null) | Decompresses a zip file into a folder; destructive because it writes files. |
| **zip_folder** | `folderPath` (string), `zipName` (string) | `path` (string/null) | Compresses a folder into a zip file; destructive because it creates a new file. |
| **read_text_file** | `filePath` (string) | `result` (string) | Reads text content from a file; supports Office and PDF formats; read-only. |
| **edit_text_file** | `filePath` (string), `oldText` (string), `newText` (string) | `result` (string) | Edits text in a file by replacing old text with new; destructive operation. |
| **search_files** | `startingDir` (string), `searchPatterns` (string), optional filters like `specifiedFileTypes`, `startDate`, `endDate` | `result` (array of objects with `name`, `path`, `dateCreated`, `snippet`) | Searches files by name, extension, date range; can return snippets for text-based files. On Copilot+ PCs, this can include semantic file search using natural language. |
