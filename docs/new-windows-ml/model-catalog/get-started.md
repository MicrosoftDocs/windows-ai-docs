---
title: Get started with Windows ML Model Catalog APIs
description: Learn how to use Windows ML Model Catalog APIs to download and share AI models across all apps on a Windows PC.
ms.topic: tutorial
ms.date: 09/26/2024
---

# Get started with Windows ML Model Catalog APIs

This guide shows you how to use the Windows ML Model Catalog APIs to manage AI models in your Windows applications. You'll learn how to set up catalog sources, find compatible models, and download them to a shared location on the device for all apps to use.

## Prerequisites

* Windows 10 PC running Windows 10 1809 or greater
* One or more model catalog sources
* [Windows App SDK 1.8.3 or greater](/windows/apps/windows-app-sdk/stable-channel) in your compatible project

## Step 1: Create a model catalog source

You must first create or obtain a model catalog source, which is an index of models including information about how to access the model. See the [Model Catalog Source docs](./model-catalog-source.md) for more information.

The Model Catalog Source JSON file can either be hosted online, referenced via `https://` endpoints, or used from a local file, referenced via file paths like `C:\Users\...`.

## Step 2: Initialize your catalog with your source

Initialize a catalog with a single catalog source:

```csharp
using Microsoft.Windows.AI.MachineLearning;
using Windows.Foundation;
using System;
using System.Threading.Tasks;

// Create a catalog source from a URI
var source = await ModelCatalogSource.CreateFromUriAsync(
    new Uri("https://contoso.com/models"));

// Create the catalog with the source
var catalog = new ModelCatalog(new ModelCatalogSource[] { source });
```

You can also configure multiple catalog sources with different priorities:

```csharp
var catalog = new ModelCatalog(new ModelCatalogSource[0]);

// Add sources in order of preference (highest priority first)
catalog.Sources.Add(await ModelCatalogSource.CreateFromUriAsync(
    new Uri("https://contoso.com/models")));
catalog.Sources.Add(await ModelCatalogSource.CreateFromUriAsync(
    new Uri("https://contoso.com/secondaryModels")));
```

## Step 3: Find a model by name

Search for a model across all configured sources:

```csharp
// Find a model by its name
CatalogModelInfo model = await catalog.FindModelAsync("phi-3.5-reasoning");

if (model != null)
{
    Console.WriteLine($"Found model: {model.Name}");
    Console.WriteLine($"Version: {model.Version}");
    Console.WriteLine($"Publisher: {model.Publisher}");
    Console.WriteLine($"Size: {model.ModelSizeInBytes / (1024 * 1024)} MB");
    Console.WriteLine($"Supported execution providers: {string.Join(", ", model.ExecutionProviders)}");
}
```

## Step 4: Download and use a model

Get a model instance and use it with your desired framework:

```csharp
// Get an instance of the model (downloads if necessary)
var progress = new Progress<double>(percent => 
    Console.WriteLine($"Download progress: {percent:P}"));

CatalogModelInstanceResult result = await model.GetInstanceAsync().AsTask(progress);

if (result.Status == CatalogModelInstanceStatus.Available)
{
    CatalogModelInstance instance = result.GetInstance();

    // Get the model path
    string modelPath = instance.ModelPaths[0];
    
    Console.WriteLine($"Model path: {modelPath}");
    
    // Inference your model using your own code
}
else
{
    Console.WriteLine($"Failed to get model: {result.ExtendedError}");
    Console.WriteLine($"Details: {result.DiagnosticText}");
}
```

## Complete example

Here's a complete example that puts it all together:

```csharp
using Microsoft.Windows.AI.MachineLearning;
using System;
using System.Threading.Tasks;

try
{
    // Create catalog with source
    var source = await ModelCatalogSource.CreateFromUriAsync(
        new Uri("https://contoso.com/models"));
    var catalog = new ModelCatalog(new ModelCatalogSource[] { source });
    
    // Find a model
    Console.WriteLine("Searching for model...");
    CatalogModelInfo model = await catalog.FindModelAsync("phi-3.5-reasoning");
    
    if (model != null)
    {
        Console.WriteLine($"Found model: {model.Name}");
        Console.WriteLine($"Version: {model.Version}");
        Console.WriteLine($"Publisher: {model.Publisher}");
        Console.WriteLine($"Size: {model.ModelSizeInBytes / (1024 * 1024)} MB");
        Console.WriteLine($"Supported execution providers: {string.Join(", ", model.ExecutionProviders)}");
        
        // Get an instance of the model (downloads if necessary)
        var progress = new Progress<double>(percent => 
            Console.WriteLine($"Download progress: {percent:P}"));
        
        CatalogModelInstanceResult result = await model.GetInstanceAsync().AsTask(progress);
        
        if (result.Status == CatalogModelInstanceStatus.Available)
        {
            CatalogModelInstance instance = result.GetInstance();

            // Get the model path
            string modelPath = instance.ModelPaths[0];
            
            Console.WriteLine($"Model path: {modelPath}");
            
            // Inference your model using your own code
        }
        else
        {
            Console.WriteLine($"Failed to get model: {result.ExtendedError}");
            Console.WriteLine($"Details: {result.DiagnosticText}");
        }
    }
    else
    {
        Console.WriteLine("Model not found");
    }
}
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
}
```

## Filtering by execution providers

Customize which execution providers to consider:

```csharp
public async Task FilterByExecutionProvidersAsync(ModelCatalog catalog)
{
    // Only look for CPU-compatible models
    catalog.ExecutionProviders.Clear();
    catalog.ExecutionProviders.Add("cpuexecutionprovider");
    
    var cpuModels = await catalog.FindAllModelAsync();
    Console.WriteLine($"Found {cpuModels.Count} CPU-compatible models");
    
    // Look for DirectML-compatible models
    catalog.ExecutionProviders.Clear();
    catalog.ExecutionProviders.Add("dmlexecutionprovider");
    
    var dmlModels = await catalog.FindAllModelAsync();
    Console.WriteLine($"Found {dmlModels.Count} DirectML-compatible models");
}
```

## Checking model status

Check if a model is already available locally:

```csharp
public void CheckModelStatus(ModelCatalog catalog)
{
    var availableModels = catalog.GetAvailableModels();
    
    foreach (var model in availableModels)
    {
        var status = model.GetStatus();
        Console.WriteLine($"Model {model.Name}: {status}");
        
        switch (status)
        {
            case CatalogModelStatus.Ready:
                Console.WriteLine("  ✓ Available locally");
                break;
            case CatalogModelStatus.NotReady:
                Console.WriteLine("  ⚠ Needs to be downloaded");
                break;
        }
    }
}
```

## Using local catalog files

Create a catalog source from a local file:

```csharp
public async Task<ModelCatalog> CreateLocalCatalogAsync()
{
    // Load catalog from a local JSON file
    var localFile = Path.Combine(Package.Current.EffectivePath, "my-models.json");
    var source = await ModelCatalogSource.CreateFromUriAsync(new Uri(localFile));
    
    var catalog = new ModelCatalog(new ModelCatalogSource[] { source });
    return catalog;
}
```

## Adding custom headers for downloads

Provide authentication or custom headers for model downloads:

```csharp
public async Task DownloadWithCustomHeadersAsync(CatalogModelInfo model)
{
    var headers = new Dictionary<string, string>
    {
        ["Authorization"] = "Bearer your-token-here",
        ["User-Agent"] = "MyApp/1.0"
    };
    
    var result = await model.GetInstanceAsync(headers);
    // Handle result...
}
```

## Error handling

Always include proper error handling when working with Model Catalog:

```csharp
public async Task<bool> SafeModelUsageAsync(string modelName)
{
    try
    {
        var source = await ModelCatalogSource.CreateFromUriAsync(
            new Uri("https://contoso.com/models"));
        var catalog = new ModelCatalog(new ModelCatalogSource[] { source });
        
        var model = await catalog.FindModelAsync(modelName);
        if (model == null)
        {
            Console.WriteLine($"Model '{modelName}' not found");
            return false;
        }
        
        var result = await model.GetInstanceAsync();
        if (result.Status != CatalogModelInstanceStatus.Available)
        {
            Console.WriteLine($"Failed to get model: {result.ExtendedError}");
            if (!string.IsNullOrEmpty(result.DiagnosticText))
            {
                Console.WriteLine($"Details: {result.DiagnosticText}");
            }
            return false;
        }
        
        using var instance = result.GetInstance();
        // Use the model...
        return true;
    }
    catch (UnauthorizedAccessException)
    {
        Console.WriteLine("Access denied - check permissions");
        return false;
    }
    catch (System.Net.Http.HttpRequestException ex)
    {
        Console.WriteLine($"Network error: {ex.Message}");
        return false;
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Unexpected error: {ex.Message}");
        return false;
    }
}
```

## Best practices

1. **Reuse catalog instances**: Reuse `ModelCatalog` instances across your app
2. **Handle download progress**: Provide user feedback during model downloads
3. **Dispose model instances**: Use `using` statements to properly dispose of model instances
4. **Check compatibility**: Verify model execution providers match your requirements
5. **Handle failures gracefully**: Always check result status before using models
6. **Use model names**: Use `FindModelAsync` with model names for automatic selection of the best model variant based on device capabilities

## Next steps

- Learn about [Model Catalog Source schema](./model-catalog-source.md)
- Explore [Windows ML overview](../overview.md)