---
title: Get Started with App Content Search in the Windows App SDK
description: Tutorial showing how to use the Windows AI AppContentIndexer API in the Windows App SDK to add AI-enhanced search capabilities based on semantic meaning and intent to your Windows app.
ms.topic: article
ms.date: 11/17/2025
---

# Get Started with App Content Search

Use App Content Search to create a semantic index of your in-app content. This allows users to find information based on meaning, rather than just keywords. The index can also be used to enhance AI assistants with domain-specific knowledge for more personalized and contextual results.

Specifically, you will learn how to use the [AppContentIndexer](/windows/windows-app-sdk/api/winrt/microsoft.windows.search.appcontentindex.appcontentindexer) API to:

> [!div class="checklist"]
>
> - Create or open an index of the content in your app
> - Add text strings to the index and then run a query
> - Manage long text string complexity
> - Index image data and then search for relevant images
> - Enable RAG (Retrieval-Augmented Generation) scenarios
> - Check indexing status and wait for completion
> - Update and remove content from the index
> - Delete an index
> - Use AppContentIndexer on a background thread
> - Close AppContentIndexer when no longer in use to release resources
> - Configure index options with GetOrCreateIndexOptions
> - Check system and index capabilities
> - Monitor index events with AppContentIndexListener
> - Handle content reindexing
> - Use content regions for structured data
> - Ingest content from text and image streams
> - Configure text and image query options
> - Use query sessions for interactive search
> - Specify language and locale
> - Query detailed indexing status
> - Check content kind support before indexing
> - Use deferred index deletion

## Prerequisites

To learn about the Windows AI API hardware requirements
and how to configure your device to successfully build
apps using the Windows AI APIs, see
[Get started building an app with Windows AI APIs](/windows/ai/apis/get-started).

The code samples in this tutorial require **Windows App
SDK 2.0.0-preview1** or later. Make sure your project
references the correct NuGet package version.

### Package Identity Requirement

Apps using **AppContentIndexer** must have package identity, which is only available to packaged apps (including those with external locations). To enable semantic indexing and [Text Recognition (OCR)](./text-recognition.md), the app must also declare the [`systemaimodels` capability](./get-started.md#build-a-new-app).

## Create or open an index of the content in your app

To create a semantic index of the content in your app, you must first establish a searchable structure that your app can use to store and retrieve content efficiently. This index acts as a local semantic and lexical search engine for your app's content.

To use the **AppContentIndexer** API, first call
`GetOrCreateIndex` with a specified index name. If an
index with that name already exists for the current app
identity and user, it is opened; otherwise, a new one
is created.

> [!TIP]
> When configuring index capabilities, be aware of
> [coupling rules](#capability-coupling-rules).
> `TextSemantic` requires `TextLexical`—attempting to
> suppress `TextLexical` while `TextSemantic` is active
> causes the suppression to be silently ignored.
> `ImageOcr` and `ImageSemantic` are independent and
> do not affect each other.

```csharp
public void SimpleGetOrCreateIndexSample()
{
    GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("myindex");
    if (!result.Succeeded)
    {
        throw new InvalidOperationException($"Failed to open index. Status = '{result.Status}', Error = '{result.ExtendedError}'");
    }
    // If result.Succeeded is true, result.Status will either be CreatedNew or OpenedExisting
    if (result.Status == GetOrCreateIndexStatus.CreatedNew)
    {
        Console.WriteLine("Created a new index");
    }
    else if(result.Status == GetOrCreateIndexStatus.OpenedExisting)
    {
        Console.WriteLine("Opened an existing index");
    }
    using AppContentIndexer indexer = result.Indexer;
    // Use indexer...
}
```

This sample shows error handling the failure case for opening an index. For simplicity, other samples in this document may not show error handling.

## Capability coupling rules

When creating an index with `GetOrCreateIndex`, you can
configure which indexing capabilities are active by
setting `IndexCapabilityRequirement` values on
`GetOrCreateIndexOptions`. Some capabilities have
dependency relationships, and the API handles these
through **coupling rules**.

### Text capabilities

- **`TextSemantic` requires `TextLexical`.**
  Semantic text indexing depends on the lexical
  pipeline. If you set
  `TextLexicalRequirement = Suppressed` but
  `TextSemanticRequirement` is `Default` or `Required`,
  the system silently treats `TextLexicalRequirement`
  as `Default`, because semantic text indexing requires
  lexical indexing to be enabled.
- `TextLexicalRequirement = Suppressed` is only
  honored when `TextSemanticRequirement` is also
  `Suppressed`.
- When both `TextLexicalRequirement` and
  `TextSemanticRequirement` are `Suppressed`, text
  content is not supported and any text regions added
  will have a region error detail of
  `UnsupportedContentKind`.

### Image capabilities

- **`ImageOcr` and `ImageSemantic` are independent
  capabilities.** Suppressing
  `ImageSemanticRequirement` does not affect OCR, and
  suppressing `ImageOcrRequirement` does not affect
  semantic image indexing.
- When both `ImageOcrRequirement` and
  `ImageSemanticRequirement` are `Suppressed`, image
  content is not supported and any image regions added
  will have a region error detail of
  `UnsupportedContentKind`.
- If `ImageOcrRequirement = Required`, lexical
  indexing will be used for extracted OCR text, even if
  `TextLexicalRequirement = Suppressed` for text
  content.

> [!NOTE]
> `GetOrCreateIndex` may return a
> `GetOrCreateIndexStatus.InvalidOptions` status when
> the specified options are invalid. Check the
> `ExtendedError` property on the result for details,
> adjust your options, and retry.

## Add text strings to the index and then run a query

This sample demonstrates how to add some text strings to the index created for your app and then run a query against that index to retrieve relevant information.

```csharp
    // This is some text data that we want to add to the index:
    Dictionary<string, string> simpleTextData = new Dictionary<string, string>
    {
        {"item1", "Here is some information about Cats: Cats are cute and fluffy. Young cats are very playful." },
        {"item2", "Dogs are loyal and affectionate animals known for their companionship, intelligence, and diverse breeds." },
        {"item3", "Fish are aquatic creatures that breathe through gills and come in a vast variety of shapes, sizes, and colors." },
        {"item4", "Broccoli is a nutritious green vegetable rich in vitamins, fiber, and antioxidants." },
        {"item5", "Computers are powerful electronic devices that process information, perform calculations, and enable communication worldwide." },
        {"item6", "Music is a universal language that expresses emotions, tells stories, and connects people through rhythm and melody." },
    };

    public void SimpleTextIndexingSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        // Add some text data to the index:
        foreach (var item in simpleTextData)
        {
            IndexableAppContent textContent = AppManagedIndexableAppContent.CreateFromString(item.Key, item.Value);
            indexer.AddOrUpdate(textContent);
        }
    }

    public void SimpleTextQueryingSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        // We search the index using a semantic query:
        AppIndexTextQuery queryCursor = indexer.CreateTextQuery("Facts about kittens.");
        IReadOnlyList<TextQueryMatch> textMatches = queryCursor.GetNextMatches(5);
        // Nothing in the index exactly matches what we queried but item1 is similar to the query so we expect
        // that to be the first match.
        foreach (var match in textMatches)
        {
            Console.WriteLine(match.ContentId);
            if (match.ContentKind == QueryMatchContentKind.AppManagedText)
            {
                AppManagedTextQueryMatch textResult = (AppManagedTextQueryMatch)match;
                // Only part of the original string may match the query. So we can use TextOffset and TextLength to extract the match.
                // In this example, we might imagine that the substring "Cats are cute and fluffy" from "item1" is the top match for the query.
                string matchingData = simpleTextData[match.ContentId];
                string matchingString = matchingData.Substring(textResult.TextOffset, textResult.TextLength);
                Console.WriteLine(matchingString);
            }
            else if (match is AppManagedOcrTextQueryMatch ocrResult)
            {
                // OCR matches come from text extracted from indexed images.
                Console.WriteLine($"OCR match: '{ocrResult.Fragment}' at {ocrResult.Subregion}");
            }
        }
    }
```

`QueryMatch` includes only `ContentId` and `TextOffset`/`TextLength`, not the matching text itself. It is your responsibility as the app developer to reference the original text. Query results are sorted by relevancy, with the top result being most relevant. Indexing occurs asynchronously, so queries may run on partial data. You can check the indexing status as outlined below.

## Manage long text string complexity

The sample demonstrates that it is not necessary for the app developer to divide the text content into smaller sections for model processing. The **AppContentIndexer** manages this aspect of complexity.

```csharp
    Dictionary<string, string> textFiles = new Dictionary<string, string>
    {
        {"file1", "File1.txt" },
        {"file2", "File2.txt" },
        {"file3", "File3.txt" },
    };
    public void TextIndexingSample2()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        var folderPath = Windows.ApplicationModel.Package.Current.InstalledLocation.Path;
        // Add some text data to the index:
        foreach (var item in textFiles)
        {
            string contentId = item.Key;
            string filename = item.Value;
            // Note that the text here can be arbitrarily large. The AppContentIndexer will take care of chunking the text
            // in a way that works effectively with the underlying model. We do not require the app author to break the text
            // down into small pieces.
            string text = File.ReadAllText(Path.Combine(folderPath, filename));
            IndexableAppContent textContent = AppManagedIndexableAppContent.CreateFromString(contentId, text);
            indexer.AddOrUpdate(textContent);
        }
    }

    public void TextIndexingSample2_RunQuery()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        var folderPath = Windows.ApplicationModel.Package.Current.InstalledLocation.Path;
        // Search the index
        AppIndexTextQuery query = indexer.CreateTextQuery("Facts about kittens.");
        IReadOnlyList<TextQueryMatch> textMatches = query.GetNextMatches(5);
        foreach (var match in textMatches)
        {
            Console.WriteLine(match.ContentId);
            if (match is AppManagedTextQueryMatch textResult)
            {
                // We load the content of the file that contains the match:
                string matchingFilename = textFiles[match.ContentId];
                string fileContent = File.ReadAllText(Path.Combine(folderPath, matchingFilename));

                // Find the substring within the loaded text that contains the match:
                string matchingString = fileContent.Substring(textResult.TextOffset, textResult.TextLength);
                Console.WriteLine(matchingString);
            }
            else if (match is AppManagedOcrTextQueryMatch ocrResult)
            {
                // Text queries can also return OCR matches from indexed images.
                Console.WriteLine($"OCR match: '{ocrResult.Fragment}' at {ocrResult.Subregion}");
            }
        }
    }
```

Text data is sourced from files, but only the content is indexed, not the files themselves. **AppContentIndexer** has no knowledge of the original files and does not monitor updates. If the file content changes, the app must update the index manually.

## Index image data and then search for relevant images

This sample demonstrates how to index image data as `SoftwareBitmaps` and then search for relevant images using text queries.

```csharp
    // We load the image data from a set of known files and send that image data to the indexer.
    // The image data does not need to come from files on disk, it can come from anywhere.
    Dictionary<string, string> imageFilesToIndex = new Dictionary<string, string>
        {
            {"item1", "Cat.jpg" },
            {"item2", "Dog.jpg" },
            {"item3", "Fish.jpg" },
            {"item4", "Broccoli.jpg" },
            {"item5", "Computer.jpg" },
            {"item6", "Music.jpg" },
        };
    public void SimpleImageIndexingSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Add some image data to the index.
        foreach (var item in imageFilesToIndex)
        {
            var file = item.Value;
            var softwareBitmap = Helpers.GetSoftwareBitmapFromFile(file);
            IndexableAppContent imageContent = AppManagedIndexableAppContent.CreateFromBitmap(item.Key, softwareBitmap);
            indexer.AddOrUpdate(imageContent);
        }
    }
    public void SimpleImageIndexingSample_RunQuery()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        // We query the index for some data to match our text query.
        AppIndexImageQuery query = indexer.CreateImageQuery("cute pictures of kittens");
        IReadOnlyList<ImageQueryMatch> imageMatches = query.GetNextMatches(5);
        // One of the images that we indexed was a photo of a cat. We expect this to be the first match to match the query.
        foreach (var match in imageMatches)
        {
            Console.WriteLine(match.ContentId);
            if (match.ContentKind == QueryMatchContentKind.AppManagedImage)
            {
                AppManagedImageQueryMatch imageResult = (AppManagedImageQueryMatch)match;
                var matchingFileName = imageFilesToIndex[match.ContentId];

                // It might be that the match is at a particular region in the image. The result may include
                // a region of interest that was the source of the match.

                Console.WriteLine($"Matching file: '{matchingFileName}' at location {imageResult.RegionOfInterest}");
            }
        }
    }
```

## Enable RAG (Retrieval-Augmented Generation) scenarios

RAG (Retrieval-Augmented Generation) involves augmenting user queries to language models with additional relevant data that the model can use for generating responses. The user's query serves as input for semantic search, which identifies pertinent information in an index. The resulting data from the semantic search is then incorporated into the prompt given to the language model so that it can generate more accurate and context-aware responses.

This sample demonstrates how you can use the **AppContentIndexer API** with an LLM to add contextual data to your app user's search query. The sample is generic, no LLM is specified and the example only queries the local data stored in the index created (no external calls to the internet). In this sample, `Helpers.GetUserPrompt()` and `Helpers.GetResponseFromChatAgent()` are not real functions and are just used to provide an example.

To enable RAG scenarios with the **AppContentIndexer** API, you can follow this example:

```csharp
    public void SimpleRAGScenario()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        // These are some text files that had previously been added to the index.
        // The key is the contentId of the item.
        Dictionary<string, string> data = new Dictionary<string, string>
        {
            {"file1", "File1.txt" },
            {"file2", "File2.txt" },
            {"file3", "File3.txt" },
        };
        string userPrompt = Helpers.GetUserPrompt();
        // We execute a query against the index using the user's prompt string as the query text.
        AppIndexTextQuery query = indexer.CreateTextQuery(userPrompt);
        IReadOnlyList<TextQueryMatch> textMatches = query.GetNextMatches(5);
        StringBuilder promptStringBuilder = new StringBuilder();
        promptStringBuilder.AppendLine("Please refer to the following pieces of information when responding to the user's prompt:");
        // For each of the matches found, we include the relevant snippets of the text files in the augmented query that we send to the language model
        foreach (var match in textMatches)
        {
            if (match is AppManagedTextQueryMatch textResult)
            {
                // We load the content of the file that contains the match:
                string matchingFilename = data[match.ContentId];
                string fileContent = File.ReadAllText(matchingFilename);
                // Find the substring within the loaded text that contains the match:
                string matchingString = fileContent.Substring(textResult.TextOffset, textResult.TextLength);
                promptStringBuilder.AppendLine(matchingString);
                promptStringBuilder.AppendLine();
            }
            else if (match is AppManagedOcrTextQueryMatch ocrResult)
            {
                // OCR matches from indexed images can also be included in the RAG context.
                promptStringBuilder.AppendLine(ocrResult.Fragment);
                promptStringBuilder.AppendLine();
            }
        }
        promptStringBuilder.AppendLine("Please provide a response to the following user prompt:");
        promptStringBuilder.AppendLine(userPrompt);
        var response = Helpers.GetResponseFromChatAgent(promptStringBuilder.ToString());
        Console.WriteLine(response);
    }
```

## Check indexing status and wait for completion

When content is added to the index, the indexing occurs asynchronously in the background. You can check the current status using `GetIndexStatistics` and query the status of a specific item using `GetContentItemStatus`.

```csharp
    public void IndexStatisticsSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        IndexStatistics indexStatistics = indexer.GetIndexStatistics();

        if (indexStatistics.IndexingInProgress)
        {
            Console.WriteLine("Items are currently being indexed in the background");
        }

        Console.WriteLine($"Total items: {indexStatistics.ItemCount}");
        Console.WriteLine($"{indexStatistics.CompletedCount} items completed.");
        Console.WriteLine($"{indexStatistics.NotStartedCount} items not started.");
        Console.WriteLine($"{indexStatistics.InProgressCount} items in progress.");
        Console.WriteLine($"{indexStatistics.ErrorsCount} items with errors.");
        Console.WriteLine($"{indexStatistics.PendingDeletionCount} items pending deletion.");
        Console.WriteLine($"{indexStatistics.RequiringReindexingCount} items require reindexing.");
    }

    public void ContentItemStatusSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        ContentItemStatusResult contentItemStatusResult = indexer.GetContentItemStatus("item1");

        if (contentItemStatusResult.Status == ContentItemStatus.NotStarted || contentItemStatusResult.Status == ContentItemStatus.InProgress)
        {
            Console.WriteLine("Indexing has not yet completed for 'item1'");
        }
        else if (contentItemStatusResult.Status == ContentItemStatus.Completed)
        {
            Console.WriteLine("Indexing has completed for 'item1'");
        }
        else if (contentItemStatusResult.Status == ContentItemStatus.Error || contentItemStatusResult.Status == ContentItemStatus.CompletedWithSomeErrors)
        {
            if (contentItemStatusResult.ErrorDetail == ContentItemErrorDetail.UnknownContentId)
            {
                Console.WriteLine("'item1' does not exist in the index");
            }
            else
            {
                Console.WriteLine($"An error occurred while indexing 'item1' {contentItemStatusResult.ExtendedError}");
            }
        }
    }
```

Before executing a query, you may want to wait for indexing to complete. Use the `WaitForIndexingIdleAsync` method to block until all content indexing operations finish or a timeout occurs.

```csharp
    public async void WaitForIndexerIdleSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Add some text data to the index:
        Dictionary<string, string> testData = GetDataForApp();
        foreach (var item in testData)
        {
            IndexableAppContent textContent = AppManagedIndexableAppContent.CreateFromString(item.Key, item.Value);
            indexer.AddOrUpdate(textContent);
        }

        // Before executing a query, wait for the indexer to go idle:
        bool isIdle = await indexer.WaitForIndexingIdleAsync(TimeSpan.FromSeconds(120));

        if (!isIdle)
        {
            Console.WriteLine("The indexer is still indexing. Query results may be incomplete");
        }

        // Execute query
        AppIndexTextQuery queryCursor = indexer.CreateTextQuery("Facts about kittens.");
    }
```

## Update and remove content from the index

You can update existing content by calling `AddOrUpdate` with the same content identifier. The new content replaces the previous version. To remove content, use `RemoveContentItem`, `RemoveContentItems`, or `RemoveAllContentItems`.

```csharp
    public void UpdateAndRemoveContentSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Update an existing item by calling AddOrUpdate with the same contentId.
        // The new content replaces the old one.
        IndexableAppContent itemToUpdate = AppManagedIndexableAppContent.CreateFromString("item3",
            "Fish live in oceans, rivers, and lakes, using their gills to extract oxygen from water.");
        indexer.AddOrUpdate(itemToUpdate);

        // Remove a single item from the index:
        indexer.RemoveContentItem("item1");

        // Remove multiple items from the index:
        indexer.RemoveContentItems(new List<string> { "item2", "item3" });

        // Remove all items from the index (the index itself still exists):
        indexer.RemoveAllContentItems();
    }
```

Items may not be removed immediately. They are scheduled for deletion and processed in the background.

## Delete an index

To permanently delete an index and its underlying database files, use `DeleteIndex`. You can list all indexes for the current app with `GetExistingIndexes`.

```csharp
    public void DeleteIndexSample()
    {
        // List all indexes for the current app:
        var indexNames = AppContentIndexer.GetExistingIndexes();
        foreach (string indexName in indexNames)
        {
            DeleteIndexResult deleteResult = AppContentIndexer.DeleteIndex(indexName,
                DeleteIndexWhileInUseBehavior.FailIfInUse);
            if (!deleteResult.Succeeded)
            {
                Console.WriteLine($"Failed to delete index '{indexName}'. Status: {deleteResult.Status}, Error: {deleteResult.ExtendedError}");
            }
        }
    }
```

If `DeleteIndex` is called while the index is still open, the behavior depends on the `DeleteIndexWhileInUseBehavior` parameter:

- **FailIfInUse** – The call fails with status `DeleteIndexStatus.IndexInUse`.
- **DeferIfInUse** – Deletion is deferred until all open instances are closed.

## Use AppContentIndexer on a background thread

An **AppContentIndexer** instance is not associated with a particular thread; it is an agile object that can operate across threads. Certain methods of **AppContentIndexer** and its related types may require considerable processing time. Therefore, it is advisable to avoid invoking **AppContentIndexer** APIs directly from the application's UI thread and instead use a background thread.

## Close AppContentIndexer when no longer in use to release resources

**AppContentIndexer** implements the `IClosable` interface to determine it's lifetime. The application should close the indexer when it is no longer in use. This allows **AppContentIndexer** to release its underlying resources.

```csharp
    public void IndexerDisposeSample()
    {
        var indexer = AppContentIndexer.GetOrCreateIndex("myindex").Indexer;
        // use indexer
        indexer.Dispose();
        // after this point, it would be an error to try to use indexer since it is now Closed.
    }
```

In C# code, the `IClosable` interface is projected as `IDisposable`. C# code can use the `using` pattern for **AppContentIndexer** instances.

```csharp
    public void IndexerUsingSample()
    {
        using var indexer = AppContentIndexer.GetOrCreateIndex("myindex").Indexer;
        // use indexer
        //indexer.Dispose() is automatically called
    }
```

If you open the same index multiple times in your app, you must call `Close` on each instance.

Opening and closing an index is an expensive operation, so you should minimize such operations in your application. For example, an application could store a single instance of the **AppContentIndexer** for the application and use that instance throughout the lifetime of the application instead of constantly opening and closing the index for each action that needs to be performed.

## Intermediate scenarios

The following intermediate samples demonstrate additional functionality of the **AppContentIndexer** API, including index configuration, capabilities, events, content regions, streams, query options, query sessions, and language support.

### Configure index options with GetOrCreateIndexOptions

When creating an index, you can specify options via `GetOrCreateIndexOptions` to control capability requirements, default language, and whether to always create a fresh index.

```csharp
    public void IndexOptionsSample()
    {
        var options = new GetOrCreateIndexOptions();

        // If an existing index with this name exists, delete it and create a new one.
        // Default is false (open the existing index if it exists).
        options.CreateAlways = true;

        // Set the default language for the index.
        // If not specified, the system chooses a default based on the user's locale.
        options.DefaultLanguage = "fr-FR";

        // Suppress OCR for this index — images will not have text extracted via OCR.
        options.ImageOcrRequirement = IndexCapabilityRequirement.Suppressed;

        // Suppress semantic text indexing — only lexical indexing and searching will be enabled.
        options.TextSemanticRequirement = IndexCapabilityRequirement.Suppressed;

        GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("myindex", options);
        if (!result.Succeeded)
        {
            Console.WriteLine($"Failed: {result.Status}, Error: {result.ExtendedError}");
            return;
        }

        using AppContentIndexer indexer = result.Indexer;

        // Re-opening an existing index without options uses the options it was created with.
        GetOrCreateIndexResult reopenResult = AppContentIndexer.GetOrCreateIndex("myindex");

        // Opening with incompatible options fails with IncompatibleWithExistingOptions.
        var incompatibleOptions = new GetOrCreateIndexOptions
        {
            DefaultLanguage = "en-US", // conflicts with "fr-FR" used at creation
        };
        GetOrCreateIndexResult failResult = AppContentIndexer.GetOrCreateIndex("myindex", incompatibleOptions);
        // failResult.Status == GetOrCreateIndexStatus.IncompatibleWithExistingOptions

        // Opening with compatible options succeeds.
        var compatibleOptions = new GetOrCreateIndexOptions
        {
            ImageOcrRequirement = IndexCapabilityRequirement.Suppressed,
        };
        GetOrCreateIndexResult okResult = AppContentIndexer.GetOrCreateIndex("myindex", compatibleOptions);
    }
```

`CreateAlways = true` deletes any existing index with the same name before creating a new one. This is useful for resetting an index or changing options that are incompatible with an existing index. If the existing index is currently open, the call fails with `AppContentIndexerFileLockedError`.

### Check system capabilities

Before creating an index, you can check what capabilities the current system supports. Use `GetIndexCapabilitiesOfCurrentSystem()` to query system-level capability status, and `GetLanguageStatusForIndexCapability()` to check language support for a specific capability.

```csharp
    public void SystemCapabilitiesSample()
    {
        IndexCapabilitiesOfCurrentSystem capabilities = AppContentIndexer.GetIndexCapabilitiesOfCurrentSystem();

        // Each status is one of: Ready, NotReady, DisabledByPolicy, or NotSupported.
        Console.WriteLine($"Lexical: {capabilities.GetIndexCapabilityStatus(IndexCapability.TextLexical)}");
        Console.WriteLine($"Semantic Text: {capabilities.GetIndexCapabilityStatus(IndexCapability.TextSemantic)}");
        Console.WriteLine($"OCR: {capabilities.GetIndexCapabilityStatus(IndexCapability.ImageOcr)}");
        Console.WriteLine($"Semantic Image: {capabilities.GetIndexCapabilityStatus(IndexCapability.ImageSemantic)}");

        // Check language support for a specific capability.
        string language = "fr-CA";
        IndexCapabilityLanguageStatus langStatus =
            capabilities.GetLanguageStatusForIndexCapability(IndexCapability.TextSemantic, language);

        if (langStatus == IndexCapabilityLanguageStatus.Supported)
        {
            Console.WriteLine($"Semantic text indexing fully supports {language}");
        }
        else if (langStatus == IndexCapabilityLanguageStatus.Partial)
        {
            Console.WriteLine($"{language} is partially supported; a fallback language may be used");
        }
        else if (langStatus == IndexCapabilityLanguageStatus.NotSupported)
        {
            Console.WriteLine($"{language} is not supported for semantic text indexing");
        }
    }
```

### Check index capabilities

After opening an index, use `GetIndexCapabilities()` to check the initialization status of each capability for that specific index. Capabilities may be suppressed by options or unavailable on the system. Use `WaitForIndexCapabilitiesAsync()` to wait for models to finish loading.

```csharp
    public async void IndexCapabilitiesSample()
    {
        using AppContentIndexer indexer = AppContentIndexer.GetOrCreateIndex("myindex").Indexer;

        // Wait for models to load — this may take time if components need to be downloaded.
        IndexCapabilities capabilities = await indexer.WaitForIndexCapabilitiesAsync();

        // Check each capability. InitializationStatus is one of:
        // Initialized, Initializing, Suppressed, NotSupported, DisabledByPolicy, InitializationError
        IndexCapabilityState textLexical = capabilities.GetCapabilityState(IndexCapability.TextLexical);
        if (textLexical.InitializationStatus == IndexCapabilityInitializationStatus.Initialized)
        {
            Console.WriteLine("Lexical text indexing is ready.");
        }

        IndexCapabilityState textSemantic = capabilities.GetCapabilityState(IndexCapability.TextSemantic);
        if (textSemantic.InitializationStatus == IndexCapabilityInitializationStatus.Initialized)
        {
            Console.WriteLine("Semantic text indexing is ready.");
        }

        IndexCapabilityState imageOcr = capabilities.GetCapabilityState(IndexCapability.ImageOcr);
        if (imageOcr.InitializationStatus == IndexCapabilityInitializationStatus.Initialized)
        {
            Console.WriteLine("OCR is ready.");
        }

        IndexCapabilityState imageSemantic = capabilities.GetCapabilityState(IndexCapability.ImageSemantic);
        if (imageSemantic.InitializationStatus == IndexCapabilityInitializationStatus.Initialized)
        {
            Console.WriteLine("Semantic image indexing is ready.");
        }

        // Check for errors across all capabilities.
        if (capabilities.HasCapabilitiesWithErrors)
        {
            foreach (var cap in capabilities.GetCapabilitiesWithErrors())
            {
                IndexCapabilityState state = capabilities.GetCapabilityState(cap);
                Console.WriteLine($"Error for {cap}: {state.InitializationStatus}, {state.ErrorMessage}");
            }
        }
    }
```

You can also call `GetIndexCapabilities()` without waiting, for a synchronous snapshot of the current state. Capabilities that are still loading will show `Initializing`.

### Monitor index events with AppContentIndexListener

The `AppContentIndexListener` provides three events to monitor changes in the index:

- **`IndexStatisticsChanged`** — Fired when indexing statistics change (items completed, errors, etc.).
- **`ContentItemStatusChanged`** — Fired when the status of individual content items changes.
- **`IndexCapabilitiesChanged`** — Fired when the initialization status of capabilities changes (for example, when a model finishes loading).

> [!NOTE]
> All listener events are raised on background threads. Dispatch to the UI thread if you need to update UI in response to these events. Events may have an internal refractory period to avoid flooding the application.

```csharp
    public void ListenerEventsSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Monitor overall indexing progress.
        indexer.Listener.IndexStatisticsChanged += (AppContentIndexer sender, IndexStatistics stats) =>
        {
            Console.WriteLine($"{stats.CompletedCount}/{stats.ItemCount} items indexed.");
            if (!stats.IndexingInProgress)
            {
                Console.WriteLine("Indexing is complete.");
            }
        };

        // Monitor individual item status changes.
        indexer.Listener.ContentItemStatusChanged += (AppContentIndexer sender,
            IReadOnlyDictionary<string, ContentItemStatusResult> changedItems) =>
        {
            foreach (var entry in changedItems)
            {
                Console.WriteLine($"Item '{entry.Key}' status: {entry.Value.Status}");
                if (entry.Value.ReindexingStatus != ContentItemReindexingStatus.None)
                {
                    Console.WriteLine($"  Reindexing needed: {entry.Value.ReindexingStatus}");
                }
            }
        };

        // Monitor capability initialization changes.
        indexer.Listener.IndexCapabilitiesChanged += (AppContentIndexer sender, IndexCapabilities caps) =>
        {
            if (caps.GetCapabilityState(IndexCapability.TextSemantic).InitializationStatus
                == IndexCapabilityInitializationStatus.Initialized)
            {
                Console.WriteLine("Semantic text capability is now ready.");
            }

            if (caps.HasCapabilitiesWithErrors)
            {
                foreach (var cap in caps.GetCapabilitiesWithErrors())
                {
                    var state = caps.GetCapabilityState(cap);
                    Console.WriteLine($"Capability error: {cap} — {state.ErrorMessage}");
                }
            }
        };
    }
```

### Handle content reindexing

Items in the index may need to be reindexed when the semantic model changes or when content was unavailable during initial ingestion. Use `GetContentItemsRequiringReindexing()` to discover affected items and re-add them with `AddOrUpdate`.

> [!IMPORTANT]
> Items are **not** automatically re-indexed. Your app must discover flagged items and call `AddOrUpdate` to trigger reprocessing.

```csharp
    public partial class ReindexingSample
    {
        // Data that was previously indexed.
        Dictionary<string, string> data = new Dictionary<string, string>
        {
            {"item1", "Cats are cute and fluffy." },
            {"item2", "Dogs are loyal companions." },
        };

        public void OnAppStartUp()
        {
            AppContentIndexer indexer = GetIndexerForApp();

            // Watch for items moving into the RequiresReindexing state.
            indexer.Listener.ContentItemStatusChanged += (AppContentIndexer sender,
                IReadOnlyDictionary<string, ContentItemStatusResult> changedItems) =>
            {
                foreach (var entry in changedItems)
                {
                    if (entry.Value.ReindexingStatus == ContentItemReindexingStatus.SemanticModelChanged ||
                        entry.Value.ReindexingStatus == ContentItemReindexingStatus.ContentUnavailable)
                    {
                        ReindexItem(indexer, entry.Key);
                    }
                }
            };

            // Reindex any items that already require it.
            ReindexAllFlaggedItems(indexer);
        }

        public void ReindexAllFlaggedItems(AppContentIndexer indexer)
        {
            if (indexer.GetIndexStatistics().RequiringReindexingCount == 0)
                return;

            ContentItemReader reader = indexer.GetContentItemsRequiringReindexing();
            IReadOnlyList<string> batch = reader.GetNextItems(100);
            while (batch.Count > 0)
            {
                foreach (string contentId in batch)
                {
                    ReindexItem(indexer, contentId);
                }
                batch = reader.GetNextItems(100);
            }
        }

        private void ReindexItem(AppContentIndexer indexer, string contentId)
        {
            if (data.TryGetValue(contentId, out string content))
            {
                indexer.AddOrUpdate(
                    AppManagedIndexableAppContent.CreateFromString(contentId, content));
            }
        }
    }
```

### Use content regions for structured data

When your app works with structured documents that contain multiple text and image elements (such as slideshows), use `AppIndexContentRegion` to index multiple regions under a single content item.

```csharp
    // Example app data model for a slideshow presentation.
    public class PresentationSlideShow
    {
        public string PresentationId { get; set; }
        public string Title { get; set; }
        public List<PresentationSlide> Slides { get; set; }
    }

    public class PresentationSlide
    {
        public List<SlideElement> Elements { get; set; }
    }

    public abstract class SlideElement
    {
        public string ElementId { get; set; }
    }

    public class SlideTextBox : SlideElement
    {
        public string Text { get; set; }
    }

    public class SlideImage : SlideElement
    {
        public SoftwareBitmap Bitmap { get; set; }
    }
```

```csharp
    public void ContentRegionIndexingSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        List<PresentationSlideShow> slideshows = Helpers.GetAllPresentationSlideshows();

        foreach (var slideshow in slideshows)
        {
            List<AppIndexContentRegion> regions = new List<AppIndexContentRegion>();

            for (int i = 0; i < slideshow.Slides.Count; i++)
            {
                foreach (var element in slideshow.Slides[i].Elements)
                {
                    // Create a unique region ID for each element.
                    string regionId = $"{i}.{element.ElementId}";

                    if (element is SlideTextBox textBox)
                    {
                        regions.Add(AppIndexContentRegion.CreateFromString(regionId, textBox.Text));
                    }
                    else if (element is SlideImage image)
                    {
                        regions.Add(AppIndexContentRegion.CreateFromBitmap(regionId, image.Bitmap));
                    }
                }
            }

            IndexableAppContent content =
                AppManagedIndexableAppContent.CreateFromContentRegions(slideshow.PresentationId, regions);
            indexer.AddOrUpdate(content);
        }
    }

    public void ContentRegionQuerySample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        List<PresentationSlideShow> slideshows = Helpers.GetAllPresentationSlideshows();

        AppIndexTextQuery textQuery = indexer.CreateTextQuery("facts about kittens");
        IReadOnlyList<TextQueryMatch> matches = textQuery.GetNextMatches(5);

        foreach (var match in matches)
        {
            // Find the slideshow and element that matched.
            var slideshow = slideshows.FirstOrDefault(s => s.PresentationId == match.ContentId);
            if (slideshow == null) continue;

            // Parse the region ID (format: "slideIndex.elementId").
            string regionId = match.RegionId;
            int dotIndex = regionId.IndexOf('.');
            int slideIndex = int.Parse(regionId.Substring(0, dotIndex));
            string elementId = regionId.Substring(dotIndex + 1);

            var element = slideshow.Slides[slideIndex].Elements
                .FirstOrDefault(e => e.ElementId == elementId);

            if (match is AppManagedTextQueryMatch textResult && element is SlideTextBox textBox)
            {
                string snippet = textBox.Text.Substring(textResult.TextOffset, textResult.TextLength);
                Console.WriteLine($"Text match in '{slideshow.Title}', slide {slideIndex}: '{snippet}'");
            }
            else if (match is AppManagedOcrTextQueryMatch ocrResult)
            {
                Console.WriteLine($"OCR match in '{slideshow.Title}', slide {slideIndex}: '{ocrResult.Fragment}'");
            }
        }
    }
```

### Ingest content from text and image streams

In addition to strings and `SoftwareBitmap` objects, you can index content from streams using `CreateFromTextStream` and `CreateFromImageStream`. This is useful when working with large files or data that is already available as a stream.

```csharp
    public void StreamIngestionSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();
        var folderPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;

        // Index text from a stream.
        string textFilePath = Path.Combine(folderPath, "document.txt");
        using (FileStream fs = new FileStream(textFilePath, FileMode.Open, FileAccess.Read))
        {
            Windows.Storage.Streams.IInputStream textStream = fs.AsInputStream();
            int byteCount = (int)fs.Length;

            AppManagedIndexableAppContent textContent =
                AppManagedIndexableAppContent.CreateFromTextStream(
                    "doc1", byteCount, AppIndexTextStreamEncoding.Utf8, textStream);
            indexer.AddOrUpdate(textContent);
        }

        // Index an image from a stream.
        string imageFilePath = Path.Combine(folderPath, "photo.jpg");
        using (FileStream fs = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
        {
            Windows.Storage.Streams.IInputStream imageStream = fs.AsInputStream();
            int byteCount = (int)fs.Length;

            AppManagedIndexableAppContent imageContent =
                AppManagedIndexableAppContent.CreateFromImageStream(
                    "img1", byteCount, imageStream);
            indexer.AddOrUpdate(imageContent);
        }
    }
```

Supported text stream encodings are `AppIndexTextStreamEncoding.Utf8` and `AppIndexTextStreamEncoding.Utf16_LE`.

### Configure text query options

Use `TextQueryOptions` to control how text queries are executed. You can configure lexical match type (fuzzy or exact), match scope, whether to suppress semantic matches, and query language.

```csharp
    public void TextQueryOptionsSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        TextQueryOptions options = new TextQueryOptions();

        // Suppress semantic matching — only lexical matches will be returned.
        options.SuppressSemanticMatches = true;

        // Constrain results to at most one match per content item.
        // Other values: QueryMatchScope.Region (one per region) or
        // QueryMatchScope.Unconstrained (multiple per region, useful for RAG).
        options.MatchScope = QueryMatchScope.ContentItem;

        // Use exact lexical matching instead of the default fuzzy matching.
        // With fuzzy matching, "improve" matches "improved" and "improving".
        // With exact matching, only "improve" matches.
        options.TextMatchType = TextLexicalMatchType.Exact;

        // Optionally specify the query language.
        options.Language = "en-US";

        AppIndexTextQuery query = indexer.CreateTextQuery("improve", options);
        IReadOnlyList<TextQueryMatch> matches = query.GetNextMatches(10);

        foreach (var match in matches)
        {
            Console.WriteLine($"Match: {match.ContentId}, Region: {match.RegionId}");
        }
    }
```

With fuzzy matching (the default), terms are matched flexibly—"improve" will match "improved", "improving", and "improvement". With exact matching, only exact word forms match. Fuzzy matching also relaxes term order and allows stop words (such as "a", "the", "in") to be optional.

### Configure image query options

Use `ImageQueryOptions` to control image query behavior. You can set the match scope and query language.

```csharp
    public void ImageQueryOptionsSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        ImageQueryOptions options = new ImageQueryOptions();

        // Constrain to at most one match per content item.
        options.MatchScope = QueryMatchScope.ContentItem;

        // Specify the query language.
        options.Language = "en-US";

        AppIndexImageQuery query = indexer.CreateImageQuery("sunset over mountains", options);
        IReadOnlyList<ImageQueryMatch> matches = query.GetNextMatches(5);

        foreach (var match in matches)
        {
            Console.WriteLine($"Image match: {match.ContentId}");
            if (match is AppManagedImageQueryMatch imageMatch)
            {
                Console.WriteLine($"  Region of interest: {imageMatch.RegionOfInterest}");
            }
        }
    }
```

### Use query sessions for interactive search

For interactive search scenarios (such as a search box that updates results as the user types), use `AppIndexTextQuerySession` or `AppIndexImageQuerySession`. Query sessions automatically cancel stale queries when the search phrase changes, so you don't need to manage background query cleanup.

```csharp
    AppIndexTextQuerySession querySession;

    public void StartTextQuerySession()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Create a text query session
        querySession = indexer.CreateTextQuerySession();
        // Want 8 matches per result (default is 10, max is 30)
        querySession.DesiredMatchesPerResult = 8;
        // Subscribe to ResultChanged BEFORE calling Start() (fires on background thread!)
        querySession.ResultChanged += QuerySession_ResultChanged;
        querySession.Start();

        SearchTextBox.TextChanged += SearchBoxTextChanged;
        //User selects a result → Stop
        SearchTextBox.QuerySubmitted += SearchBox_QuerySubmitted;
    }

    private void SearchBoxTextChanged(AutoSuggestBox sender, AutoSuggestBoxTextChangedEventArgs args)
    {
        querySession.UpdateQueryPhrase(sender.Text);
    }

    private void QuerySession_ResultChanged(AppIndexTextQuerySession sender, Object args)
    {
        // This event fires on a background thread — dispatch to UI thread.
        this.DispatcherQueue.TryEnqueue(() =>
        {
            if (querySession != null)
            {
                TextQuerySessionResult result = querySession.GetResult();
                if (result.IsValid)
                {
                    DisplaySearchResultsInUI(result.Matches);
                }
            }
        });
    }

    private void SearchBox_QuerySubmitted(AutoSuggestBox sender, AutoSuggestBoxQuerySubmittedEventArgs args)
    {
        var chosenMatch = args.ChosenSuggestion as TextQueryMatch;

        // Providing the chosen match helps the system improve future results.
        querySession.Stop(chosenMatch);
        querySession.Dispose();
        querySession = null;
    }
```

You can start a query session with options and an initial query phrase using the `Start(TextQueryOptions, String)` overload. The `DesiredMatchesPerResult` property controls how many matches each result contains (up to `MaxMatchesPerResult`). Changes to this property take effect on the next `Start` or `UpdateQueryPhrase` call.

You can also create an image query session following the same pattern with `CreateImageQuerySession`:

```csharp
    AppIndexImageQuerySession imageQuerySession;

    public void StartImageQuerySession()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        imageQuerySession = indexer.CreateImageQuerySession();
        imageQuerySession.DesiredMatchesPerResult = 8;
        imageQuerySession.ResultChanged += ImageQuerySession_ResultChanged;
        imageQuerySession.Start();
    }

    private void ImageQuerySession_ResultChanged(AppIndexImageQuerySession sender, Object args)
    {
        this.DispatcherQueue.TryEnqueue(() =>
        {
            if (imageQuerySession != null)
            {
                ImageQuerySessionResult result = imageQuerySession.GetResult();
                if (result.IsValid)
                {
                    DisplayImageSearchResultsInUI(result.Matches);
                }
            }
        });
    }
```

### Specify language and locale

**AppContentIndexer** supports multiple languages. You can set a default language for the index, specify the language of individual content items, and specify the query language.

```csharp
    public void LanguageAndLocaleSample()
    {
        // Set a default language when creating the index.
        var options = new GetOrCreateIndexOptions { DefaultLanguage = "fr-FR" };
        GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("multilingual-index", options);

        if (result.Status == GetOrCreateIndexStatus.UnsupportedDefaultLanguage)
        {
            Console.WriteLine("The specified default language is not supported.");
            return;
        }

        using AppContentIndexer indexer = result.Indexer;
        Console.WriteLine($"Index default language: {indexer.DefaultLanguage}");

        // Specify the language for a specific content item.
        ContentRegionTextOptions textOptions = new ContentRegionTextOptions { Language = "es-ES" };
        var spanishItem = AppManagedIndexableAppContent.CreateFromString(
            "spanish-doc", "Los gatos son animales adorables y juguetones.", textOptions);
        indexer.AddOrUpdate(spanishItem);

        // Specify the query language.
        TextQueryOptions queryOptions = new TextQueryOptions { Language = "es-ES" };
        AppIndexTextQuery query = indexer.CreateTextQuery("gatos juguetones", queryOptions);
        IReadOnlyList<TextQueryMatch> matches = query.GetNextMatches(5);

        foreach (var match in matches)
        {
            Console.WriteLine($"Match: {match.ContentId}");
        }
    }
```

If a language is partially supported, the system may fall back to a related language (for example, `fr-CA` may fall back to `fr-FR`). If a language is not supported for a capability, functionality is reduced for content in that language.

### Create text content with region options

Use `ContentRegionTextOptions` to specify the language for individual text content items when they differ from the index default.

```csharp
    public void TextWithRegionOptionsSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // The index has a default language, but this item is in French.
        ContentRegionTextOptions frenchOptions = new ContentRegionTextOptions
        {
            Language = "fr-FR"
        };

        var frenchContent = AppManagedIndexableAppContent.CreateFromString(
            "french-doc",
            "Les chiens sont des animaux loyaux et affectueux.",
            frenchOptions);
        indexer.AddOrUpdate(frenchContent);

        // You can also use text options with content regions.
        ContentRegionTextOptions germanOptions = new ContentRegionTextOptions
        {
            Language = "de-DE"
        };

        var germanRegion = AppIndexContentRegion.CreateFromString(
            "german-section",
            "Katzen sind niedliche und flauschige Tiere.",
            germanOptions);

        var multiLangContent = AppManagedIndexableAppContent.CreateFromContentRegions(
            "multi-lang-doc",
            new List<AppIndexContentRegion> { germanRegion });
        indexer.AddOrUpdate(multiLangContent);
    }
```

## Advanced scenarios

The following advanced samples demonstrate fine-grained status querying, content kind validation, and deferred deletion patterns.

### Query detailed indexing status

Use `ContentItemReader` and `QueryContentItemsFilterFlags` to query items by their indexing status. Use `GetContentItemStatuses` to retrieve status for multiple items in a single call.

```csharp
    public void DetailedStatusSample()
    {
        AppContentIndexer indexer = GetIndexerForApp();

        // Get all content items and display their status.
        ContentItemReader allItems = indexer.GetContentItems();
        IReadOnlyList<string> batch = allItems.GetNextItems(100);

        while (batch.Count > 0)
        {
            // Retrieve status for the entire batch at once.
            IReadOnlyList<ContentItemStatusResult> statuses = indexer.GetContentItemStatuses(batch);

            for (int i = 0; i < batch.Count; i++)
            {
                Console.WriteLine($"Item '{batch[i]}': Status={statuses[i].Status}, " +
                    $"Error={statuses[i].ErrorDetail}, Reindexing={statuses[i].ReindexingStatus}");
            }

            batch = allItems.GetNextItems(100);
        }

        // Query items by specific filter flags.
        Console.WriteLine("\nItems with errors:");
        ContentItemReader errorItems = indexer.GetContentItems(QueryContentItemsFilterFlags.WithErrors);
        IReadOnlyList<string> errorBatch = errorItems.GetNextItems(100);
        while (errorBatch.Count > 0)
        {
            foreach (string contentId in errorBatch)
            {
                ContentItemStatusResult status = indexer.GetContentItemStatus(contentId);
                Console.WriteLine($"  {contentId}: {status.ErrorDetail} — {status.ExtendedError}");
            }
            errorBatch = errorItems.GetNextItems(100);
        }

        Console.WriteLine("\nItems pending deletion:");
        ContentItemReader pendingDeletion =
            indexer.GetContentItems(QueryContentItemsFilterFlags.PendingDeletion);
        foreach (string contentId in pendingDeletion)
        {
            Console.WriteLine($"  {contentId}");
        }

        Console.WriteLine("\nItems requiring reindexing:");
        ContentItemReader needsReindex =
            indexer.GetContentItems(QueryContentItemsFilterFlags.RequiringReindexing);
        foreach (string contentId in needsReindex)
        {
            Console.WriteLine($"  {contentId}");
        }
    }
```

`ContentItemReader` implements `IIterable<String>`, so you can use it directly in a `foreach` loop or call `GetNextItems` to process items in batches.

### Check content kind support before indexing

Before adding content, you can check whether the index supports a particular content kind using `IsContentKindSupported`. This returns `false` when all capabilities for that content kind are suppressed.

```csharp
    public void IsContentKindSupportedSample()
    {
        // Create an index with all image capabilities suppressed.
        var options = new GetOrCreateIndexOptions
        {
            ImageOcrRequirement = IndexCapabilityRequirement.Suppressed,
            ImageSemanticRequirement = IndexCapabilityRequirement.Suppressed,
        };

        GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("text-only-index", options);
        if (!result.Succeeded) return;

        using AppContentIndexer indexer = result.Indexer;

        // Check before adding content.
        if (indexer.IsContentKindSupported(RegionContentKind.Text))
        {
            var textContent = AppManagedIndexableAppContent.CreateFromString(
                "doc1", "Text content is supported.");
            indexer.AddOrUpdate(textContent);
        }

        if (!indexer.IsContentKindSupported(RegionContentKind.Image))
        {
            Console.WriteLine("Image content is not supported in this index. " +
                "Both ImageOcr and ImageSemantic are suppressed.");
            // Adding an image would throw an ArgumentException.
        }
    }
```

### Use deferred index deletion

When calling `DeleteIndex`, if the index is still open by other callers, you can use `DeferIfInUse` to schedule deletion for when all instances are closed.

```csharp
    public void DeferredDeletionSample()
    {
        // Open the index.
        GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("myindex");
        AppContentIndexer indexer = result.Indexer;

        // Attempt to delete while the index is still open.
        // With FailIfInUse, this would fail with DeleteIndexStatus.IndexInUse.
        // With DeferIfInUse, the deletion is scheduled for after all instances close.
        DeleteIndexResult deleteResult = AppContentIndexer.DeleteIndex(
            "myindex", DeleteIndexWhileInUseBehavior.DeferIfInUse);

        if (deleteResult.Status == DeleteIndexStatus.Deferred)
        {
            Console.WriteLine("Deletion deferred until all index instances are closed.");
        }

        // When we close the last instance, the index will be deleted.
        indexer.Dispose();

        // The next GetOrCreateIndex call will create a fresh index.
        GetOrCreateIndexResult freshResult = AppContentIndexer.GetOrCreateIndex("myindex");
        Console.WriteLine($"New index status: {freshResult.Status}");
        // freshResult.Status will be CreatedNew
        freshResult.Indexer?.Dispose();
    }
```

If deferred deletion fails (for example, due to a file system error), the index is still marked as pending deletion. The next call to `GetOrCreateIndex` will attempt to delete it and create a new index, behaving as if `CreateAlways` were set to `true`.
