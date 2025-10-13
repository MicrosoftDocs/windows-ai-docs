---
title: Windows ML Model Catalog Source schema reference
description: Reference documentation for the Windows ML Model Catalog Source JSON schema used to define model catalog sources.
ms.topic: reference
ms.date: 09/26/2024
---

# Windows ML Model Catalog Source schema reference

This page provides reference documentation for the JSON schema used by Windows ML Model Catalog Source to define model catalog sources. Model catalog sources are JSON files that describe available AI models, their compatibility, and download information.

Your Model Catalog Source file can be hosted online at `https://` URLs, or referenced as a local file from your app.

## Schema overview

A model catalog is a JSON file that contains an array of model definitions and optional metadata. The schema follows standard JSON Schema conventions and is designed to be both human-readable and machine-parsable.

The catalog supports two types of model distribution:
- **File-based models**: Models distributed as individual files (ONNX models, configs, etc.)
- **Package-based models**: Models distributed as Windows packages via Store or other package managers

## Root catalog structure

```json
{
  "base": "https://contoso.com/catalog-docs-link",
  "models": [
    // Array of model objects
  ]
}
```

### Root properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `base` | string (URI) | Yes | URL for the catalog API reference documentation |
| `models` | array | Yes | Array of model definitions |

## Model object structure

Each model in the `models` array follows this structure:

```json
{
  "id": "unique-model-id",
  "alias": "model-alias",
  "name": "Model Display Name",
  "version": "1.0.0",
  "modelType": "ONNX",
  "publisher": "Publisher Name",
  "executionProviders": "CPUExecutionProvider",
  "description": "Model description",
  "modelSizeBytes": 13632917530,
  "license": "MIT",
  "licenseUri": "https://opensource.org/licenses/MIT",
  "licenseText": "License text content",
  "uri": "https://models.example.com/model-base",
  "files": [
    {
      "name": "model.onnx",
      "uri": "https://models.example.com/model.onnx",
      "sha256": "d7f93e79ba1284a3ff2b4cea317d79f3e98e64acfce725ad5f4e8197864aef73"
    }
  ]
}
```

### Model properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `id` | string | Yes | Unique identifier for this specific model |
| `alias` | string | No | Short alias name for the model |
| `name` | string | Yes | Full name of the model |
| `version` | string | Yes | Model version number |
| `modelType` | string | No | Type of model (currently only "ONNX" supported) |
| `publisher` | string | Yes | Publisher or organization that created the model |
| `executionProviders` | string | Yes | Comma-separated list of execution providers supported by the model |
| `description` | string | No | Detailed description of the model |
| `modelSizeBytes` | integer | No | Size of the model in bytes (minimum: 0) |
| `license` | string | Yes | License type (e.g., "MIT", "BSD", "Apache") |
| `licenseUri` | string | Yes | URI to the license document |
| `licenseText` | string | No | Text content of the license |
| `uri` | string | No | Base URI where the model can be accessed |
| `files` | array | Conditional* | Array of files associated with the model |
| `packages` | array | Conditional* | Array of packages required for the model |

> **Note**: A model must have either `files` OR `packages`, but not both.

### Execution providers

The `executionProviders` field contains a comma-separated list of execution provider names:

```json
"executionProviders": "CPUExecutionProvider,DmlExecutionProvider"
```

**Common execution provider names:**

| Provider Name | Description |
|---------------|-------------|
| `CPUExecutionProvider` | CPU execution (always supported) |
| `QNNExecutionProvider` | Qualcomm AI Engine (NPU) |
| `OpenVINOExecutionProvider` | Intel OpenVINO acceleration |
| `DmlExecutionProvider` | DirectML (GPU acceleration) |

### File object

Files are used to distribute individual model components (ONNX files, configurations, etc.):

```json
{
  "name": "model.onnx",
  "uri": "https://models.example.com/model.onnx",
  "sha256": "d7f93e79ba1284a3ff2b4cea317d79f3e98e64acfce725ad5f4e8197864aef73"
}
```

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | Yes | Name of the file |
| `uri` | string | No | URI where the file can be downloaded |
| `sha256` | string | Yes | SHA256 hash (64-character hex string) for integrity verification |

> **Note**: If `uri` is not specified, the file URI is constructed from the model's base `uri` property.

### Package object

Packages are used to distribute models as Windows packages or applications:

```json
{
  "packageFamilyName": "Microsoft.Example_8wekyb3d8bbwe",
  "uri": "ms-windows-store://pdp/?ProductId=9EXAMPLE123",
  "sha256": "a1b2c3d4e5f6789..."
}
```

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `packageFamilyName` | string | Yes | Windows package family name identifier |
| `uri` | string | Yes | URI where the package can be obtained |
| `sha256` | string | Conditional* | SHA256 hash for integrity verification |

> **Note**: `sha256` is required for HTTPS URIs but optional for other URI schemes (like `ms-windows-store://`).

### Distribution methods

The catalog supports several distribution methods:

**File-based distribution:**
- Direct HTTPS downloads
- Files hosted on GitHub, Azure, or other web servers
- Model files (`.onnx`), configuration files (`.json`), and supporting assets

**Package-based distribution:**
- Microsoft Store packages (`ms-windows-store://` URIs)
- Direct package downloads via HTTPS
- MSIX/APPX bundles and individual packages

## Complete examples

### File-based model example

Here's an example catalog with file-based models:

```json
{
  "base": "https://learn.microsoft.com/windows/ai/model-catalog",
  "models": [
    {
      "id": "squeezenet-v1",
      "alias": "squeezenet",
      "name": "SqueezeNet Image Classifier",
      "version": "1.0",
      "modelType": "ONNX",
      "publisher": "WindowsAppSDK",
      "executionProviders": "CPUExecutionProvider",
      "description": "Lightweight CNN for image classification",
      "modelSizeBytes": 13632917530,
      "license": "BSD",
      "licenseUri": "https://github.com/microsoft/WindowsAppSDK-Samples/raw/main/LICENSE",
      "licenseText": "BSD 3-Clause License",
      "uri": "https://github.com/microsoft/WindowsAppSDK-Samples/raw/main/models",
      "files": [
        {
          "name": "SqueezeNet.onnx",
          "uri": "https://github.com/microsoft/WindowsAppSDK-Samples/raw/main/models/SqueezeNet.onnx",
          "sha256": "d7f93e79ba1284a3ff2b4cea317d79f3e98e64acfce725ad5f4e8197864aef73"
        },
        {
          "name": "Labels.txt",
          "uri": "https://github.com/microsoft/WindowsAppSDK-Samples/raw/main/models/Labels.txt", 
          "sha256": "dc1fd0d4747096d3aa690bd65ec2f51fdb3e5117535bfbce46fa91088a8f93a9"
        }
      ]
    }
  ]
}
```

### Package-based model example

Here's an example catalog with package-based models:

```json
{
  "base": "https://learn.microsoft.com/windows/ai/model-catalog",
  "models": [
    {
      "id": "example-store-model",
      "alias": "store-model",
      "name": "Example Store Model",
      "version": "2.0.0",
      "modelType": "ONNX", 
      "publisher": "Microsoft",
      "executionProviders": "CPUExecutionProvider,DmlExecutionProvider",
      "license": "MIT",
      "licenseUri": "https://opensource.org/licenses/MIT",
      "licenseText": "MIT License - see URI for full text",
      "packages": [
        {
          "packageFamilyName": "Microsoft.ExampleAI_8wekyb3d8bbwe",
          "uri": "ms-windows-store://pdp/?ProductId=9NEXAMPLE123"
        }
      ]
    }
  ]
}
```

## Schema validation

The Windows ML Model Catalog follows the JSON Schema specification (draft 2020-12). You can validate your catalog files against the official schema to ensure compatibility.

### Key validation rules

1. **Required fields**: Every model must have `id`, `name`, `version`, `publisher`, `executionProviders`, and `license`
2. **Exclusive distribution**: Models must specify either `files` OR `packages`, but not both
3. **SHA256 format**: All SHA256 hashes must be exactly 64 hexadecimal characters
4. **Execution providers**: Must be objects with at least a `name` property
5. **URI format**: All URIs must be properly formatted according to RFC 3986
6. **HTTPS requirements**: Package downloads via HTTPS must include SHA256 hashes

### Common validation errors

- **Missing required fields**: Ensure all required properties are present
- **Invalid SHA256**: Check that hash values are exactly 64 hex characters
- **Mixed distribution**: Don't specify both `files` and `packages` for the same model
- **Invalid URIs**: Verify all URIs are properly formatted and accessible
- **Missing SHA256 for HTTPS packages**: HTTPS package downloads require integrity verification

## Creating your catalog

### Step 1: Choose distribution method

**Use file-based distribution when:**
- Your models are standard ONNX files with supporting assets
- You have web hosting for model files
- You want simple HTTPS downloads
- Models are relatively small (< 1GB per file)

**Use package-based distribution when:**
- Your models require system integration
- You're distributing through Microsoft Store
- Models include execution providers or dependencies
- You need Windows package management features

### Step 2: Structure your models

```json
{
  "models": [
    {
      "id": "unique-identifier-v1.0",
      "name": "Human Readable Model Name",
      "version": "1.0.0",
      "publisher": "YourOrganization",
      "executionProviders": "CPUExecutionProvider",
      "license": "MIT"
      // Add files[] or packages[] here
    }
  ]
}
```

### Step 3: Add distribution details

**For file-based models:**
```json
"uri": "https://yourserver.com/models/base-path",
"files": [
  {
    "name": "model.onnx",
    "sha256": "your-calculated-sha256-hash"
  }
]
```

**For package-based models:**
```json
"packages": [
  {
    "packageFamilyName": "YourPublisher.YourApp_randomstring", 
    "uri": "ms-windows-store://pdp/?ProductId=YourProductId"
  }
]
```

### Step 4: Test your catalog

1. **Validate JSON syntax** using a JSON validator
2. **Verify all URIs** are accessible and return correct content
3. **Check SHA256 hashes** match the actual file contents
4. **Test model downloading** using the Windows ML Model Catalog APIs

## Best practices

1. **Use semantic versioning**: Follow semantic versioning (e.g., "1.2.3") for the `version` field
2. **Provide accurate SHA256 hashes**: Always include correct SHA256 hashes for integrity verification
3. **Consistent naming**: Use consistent naming conventions for aliases and IDs across model versions
4. **Clear descriptions**: Write helpful descriptions that explain model capabilities and use cases
5. **Proper licensing**: Always include complete license information (type, URI, and text)
6. **Test accessibility**: Ensure all URIs are accessible and return the expected content
7. **Execution provider compatibility**: Ensure execution providers match target hardware capabilities
8. **Logical grouping**: Use aliases to group related model variants (different EP versions of the same base model)
9. **File organization**: Keep related files together and use descriptive file names
10. **Security**: Use HTTPS for all file downloads and include SHA256 verification

## Hosting considerations

When hosting a model catalog:

1. **HTTPS required**: Always serve catalogs and models over HTTPS
2. **CORS headers**: Configure appropriate CORS headers for cross-origin access
3. **Content-Type**: Serve catalog JSON with `application/json` content type
4. **Cache headers**: Set appropriate cache headers for performance
5. **Authentication**: Support standard HTTP authentication if required

## JSON Schema

The following is the JSON schema that can be used for validation of your JSON payload.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "WinML Model Catalog Schema",
  "description": "JSON schema for WindowsML Model catalog configuration",
  "type": "object",
  "required": [ "base", "models" ],
  "properties": {
    "base": {
      "type": "string",
      "format": "uri",
      "description": "Base URL for the catalog API reference"
    },
    "models": {
      "type": "array",
      "description": "Array of machine learning models in the catalog",
      "items": {
        "$ref": "#/$defs/Model"
      }
    }
  },
  "$defs": {
    "Model": {
      "type": "object",
      "required": [ "id", "name", "version", "publisher", "executionProviders", "license", "licenseUri" ],
      "properties": {
        "id": {
          "type": "string",
          "description": "Unique identifier for the model"
        },
        "alias": {
          "type": "string",
          "description": "Short alias name for the model"
        },
        "name": {
          "type": "string",
          "description": "Full name of the model"
        },
        "version": {
          "type": "string",
          "description": "Version of the model"
        },
        "modelType": {
          "type": "string",
          "enum": [ "ONNX" ],
          "description": "Type of machine learning model"
        },
        "publisher": {
          "type": "string",
          "description": "Publisher or organization that created the model"
        },
        "executionProviders": {
          "type": "string",
          "description": "Comma-separated list of execution providers supported by the model"
        },
        "description": {
          "type": "string",
          "description": "Detailed description of the model"
        },
        "modelSizeBytes": {
          "type": "integer",
          "minimum": 0,
          "description": "Size of the model in bytes"
        },
        "license": {
          "type": "string",
          "description": "License type (e.g., MIT, BSD, Apache)"
        },
        "licenseUri": {
          "type": "string",
          "format": "uri",
          "description": "URI to the license document"
        },
        "licenseText": {
          "type": "string",
          "description": "Text content of the license"
        },
        "uri": {
          "type": "string",
          "format": "uri",
          "description": "URI where the model can be accessed"
        },
        "files": {
          "type": "array",
          "description": "Array of files associated with the model",
          "items": {
            "$ref": "#/$defs/File"
          }
        },
        "packages": {
          "type": "array",
          "description": "Array of packages required for the model",
          "items": {
            "$ref": "#/$defs/Package"
          }
        }
      },
      "oneOf": [
        {
          "required": [ "files" ],
          "not": { "required": [ "packages" ] }
        },
        {
          "required": [ "packages" ],
          "not": { "required": [ "files" ] }
        }
      ]
    },
    "File": {
      "type": "object",
      "required": [ "name", "sha256" ],
      "properties": {
        "name": {
          "type": "string",
          "description": "Name of the file"
        },
        "uri": {
          "type": "string",
          "format": "uri",
          "description": "URI where the file can be downloaded"
        },
        "sha256": {
          "type": "string",
          "pattern": "^[a-fA-F0-9]{64}$",
          "description": "SHA256 hash of the file for integrity verification"
        }
      }
    },
    "Package": {
      "type": "object",
      "required": [ "packageFamilyName", "uri" ],
      "properties": {
        "packageFamilyName": {
          "type": "string",
          "description": "Windows package family name identifier"
        },
        "uri": {
          "type": "string",
          "format": "uri",
          "description": "URI where the package can be obtained"
        },
        "sha256": {
          "type": "string",
          "pattern": "^[a-fA-F0-9]{64}$",
          "description": "SHA256 hash of the package for integrity verification"
        }
      },
      "if": {
        "properties": {
          "uri": {
            "pattern": "^https://"
          }
        }
      },
      "then": {
        "required": [ "packageFamilyName", "uri", "sha256" ]
      }
    }
  }
}
```

## Next steps

- Learn about [Model Catalog overview](./overview.md)
- Follow the [Get started guide](./get-started.md)
- Explore [Windows ML documentation](../overview.md)