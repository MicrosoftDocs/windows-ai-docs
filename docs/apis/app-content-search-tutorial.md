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
> - Refine search results with query options
> - Manage long text string complexity
> - Index image data and then search for relevant images
> - Enable RAG (Retrieval-Augmented Generation) scenarios
> - Search as you type with a query session
> - Control indexing behavior with index options
> - Monitor indexing status and reindex flagged items
> - Remove content and delete an index
> - Use AppContentIndexer on a background thread
> - Close AppContentIndexer when no longer in use to release resources

## Prerequisites

To learn about the Windows AI API hardware requirements and how to configure your device to successfully build apps using the Windows AI APIs, see [Get started building an app with Windows AI APIs](/windows/ai/apis/get-started).

### Package Identity Requirement

Apps using **AppContentIndexer** must have package identity, which is only available to packaged apps (including those with external locations). To enable semantic indexing and [Text Recognition (OCR)](./text-recognition.md), the app must also declare the [`systemaimodels` capability](./get-started.md#build-a-new-app).

## Create or open an index of the content in your app

To create a semantic index of the content in your app, you must first establish a searchable structure that your app can use to store and retrieve content efficiently. This index acts as a local semantic and lexical search engine for your app's content.

To use the **AppContentIndexer** API, first call `GetOrCreateIndex` with a specified index name. If an index with that name already exists for the current app identity and user, it is opened; otherwise, a new one is created.

All App Content Search types are in the `Microsoft.Windows.Search.AppContentIndex` namespace.

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

The following samples use a `GetIndexerForApp` helper method to get or create an index. Here's the implementation:

```csharp
private AppContentIndexer GetIndexerForApp()
{
    var result = AppContentIndexer.GetOrCreateIndex("myindex");
    if (!result.Succeeded)
    {
        throw new InvalidOperationException($"Failed to open index. Status = '{result.Status}', Error = '{result.ExtendedError}'");
    }
    // Caller should dispose the returned indexer when finished (for example: `using var indexer = GetIndexerForApp();`).
    return result.Indexer;
}
```

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
    using AppContentIndexer indexer = GetIndexerForApp();
        // Add some text data to the index:
        foreach (var item in simpleTextData)
        {
            IndexableAppContent textContent = AppManagedIndexableAppContent.CreateFromString(item.Key, item.Value);
            indexer.AddOrUpdate(textContent);
        }
    }

    public void SimpleTextQueryingSample()
    {
        using AppContentIndexer indexer = GetIndexerForApp();
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
        }
    }
```

`QueryMatch` includes only `ContentId` and `TextOffset`/`TextLength`, not the matching text itself. It is your responsibility as the app developer to reference the original text. Query results are sorted by relevancy, with the top result being most relevant. Indexing occurs asynchronously, so queries may run on partial data. You can check the indexing status as outlined below.

> [!TIP]
> To index many items at once, call `BatchAddOrUpdate` with a collection of `IndexableAppContent`. It commits the items in a single transaction (amortizing per-call overhead) and returns a `BatchAddOrUpdateResult` whose `ItemResults` report per-item success in input order.

## Refine search results with query options

Pass `TextQueryOptions` (or `ImageQueryOptions`) to control how matches are produced and returned:

- **`MatchScope`** – limit results to at most one per content item, at most one per region, or unconstrained (multiple matches per region, which suits RAG).
- **`TextMatchType`** – choose `Exact` or `Fuzzy` lexical matching (text queries only).
- **`SuppressSemanticMatches`** – return lexical matches only (text queries only).

```csharp
public void TextQueryWithOptionsSample()
{
    using AppContentIndexer indexer = GetIndexerForApp();
    var options = new TextQueryOptions();
    options.MatchScope = QueryMatchScope.ContentItem;   // at most one match per item
    options.TextMatchType = TextLexicalMatchType.Exact; // exact lexical matching
    AppIndexTextQuery query = indexer.CreateTextQuery("kittens", options);
    IReadOnlyList<TextQueryMatch> matches = query.GetNextMatches(5);
    foreach (var match in matches)
    {
        Console.WriteLine(match.ContentId);
    }
}
```

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
    using AppContentIndexer indexer = GetIndexerForApp();
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
    using AppContentIndexer indexer = GetIndexerForApp();
        var folderPath = Windows.ApplicationModel.Package.Current.InstalledLocation.Path;
        // Search the index
        AppIndexTextQuery query = indexer.CreateTextQuery("Facts about kittens.");
        IReadOnlyList<TextQueryMatch> textMatches = query.GetNextMatches(5);
        if (textMatches != null) 
        {
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
    using AppContentIndexer indexer = GetIndexerForApp();

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
    using AppContentIndexer indexer = GetIndexerForApp();
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

                // The match may correspond to a particular region within the image. RegionOfInterest
                // contains that region, or is empty when the whole image is considered relevant.

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
    using AppContentIndexer indexer = GetIndexerForApp();
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
        }
        promptStringBuilder.AppendLine("Please provide a response to the following user prompt:");
        promptStringBuilder.AppendLine(userPrompt);
        var response = Helpers.GetResponseFromChatAgent(promptStringBuilder.ToString());
        Console.WriteLine(response);
    }
```

For richer context, keep `MatchScope` at `QueryMatchScope.Unconstrained` (the default) so that multiple relevant passages per region are returned to the language model. Set `QueryMatchScope.Region` or `QueryMatchScope.ContentItem` instead when you want to limit results to a single match per region or per content item.

## Search as you type with a query session

For search boxes where the query changes as the user types, use a query session instead of one-shot queries. Update the phrase at any time; each update cancels the previous in-flight query, and a `ResultChanged` event fires when new results are ready. Subscribe to `ResultChanged` before the first `Start` call.

```csharp
public void TextQuerySessionSample()
{
    using AppContentIndexer indexer = GetIndexerForApp();
    using AppIndexTextQuerySession session = indexer.CreateTextQuerySession();
    session.ResultChanged += (sender, args) =>
    {
        TextQuerySessionResult result = session.GetResult();
        if (result.IsValid)
        {
            foreach (TextQueryMatch match in result.Matches)
            {
                // Marshal to the UI thread if you need to update UI.
                Console.WriteLine(match.ContentId);
            }
        }
    };
    session.Start();
    // As the user types:
    session.UpdateQueryPhrase("kit");
    session.UpdateQueryPhrase("kitten");
    // When finished:
    session.Stop();
}
```

A query session can use more resources than a one-shot query, so prefer `CreateTextQuery`/`CreateImageQuery` when the query phrase doesn't change. An equivalent `CreateImageQuerySession` is available for image search.

## Control indexing behavior with index options

By default, an index uses whichever capabilities are available on the device: lexical text, semantic text, image OCR, and semantic image. To require, suppress, or configure a capability—or to set the default content language—pass `GetOrCreateIndexOptions` to `GetOrCreateIndex`.

```csharp
public void GetOrCreateIndexWithOptionsSample()
{
    var options = new GetOrCreateIndexOptions();
    // Fail to open instead of silently falling back to lexical-only if the device can't do semantic text indexing.
    options.TextSemanticRequirement = IndexCapabilityRequirement.Required;
    // Language used for content items that don't specify their own.
    options.DefaultLanguage = "en-US";

    GetOrCreateIndexResult result = AppContentIndexer.GetOrCreateIndex("myindex", options);
    if (!result.Succeeded)
    {
        // For example, Status is SemanticModelsNotAvailable when a Required capability is unavailable.
        Console.WriteLine($"Failed to open index. Status = '{result.Status}'");
        return;
    }
    using AppContentIndexer indexer = result.Indexer;
    // Use indexer...
}
```

Semantic and OCR models may be unavailable, disabled by policy, or still downloading, so check what the system and index support before relying on them.

```csharp
public void CheckCapabilitiesSample()
{
    // System-wide capability availability.
    IndexCapabilitiesOfCurrentSystem systemCapabilities = AppContentIndexer.GetIndexCapabilitiesOfCurrentSystem();
    IndexCapabilityOfCurrentSystemStatus semanticTextStatus =
        systemCapabilities.GetIndexCapabilityStatus(IndexCapability.TextSemantic);
    Console.WriteLine($"System semantic text status: {semanticTextStatus}");

    // Capability status for a specific open index instance.
    using AppContentIndexer indexer = GetIndexerForApp();
    IndexCapabilities indexCapabilities = indexer.GetIndexCapabilities();
    if (indexCapabilities.HasCapabilitiesWithErrors)
    {
        foreach (IndexCapability capability in indexCapabilities.GetCapabilitiesWithErrors())
        {
            Console.WriteLine($"Capability with error: {capability}");
        }
    }
}
```

## Monitor indexing status and reindex flagged items

Indexing runs asynchronously, so queries may return results before all content is indexed. Use these APIs to observe progress and health.

```csharp
public void IndexStatusSample()
{
    using AppContentIndexer indexer = GetIndexerForApp();

    // Overall statistics for the index.
    IndexStatistics statistics = indexer.GetIndexStatistics();
    Console.WriteLine($"Items: {statistics.ItemCount}, indexing in progress: {statistics.IndexingInProgress}");

    // Status of a single content item.
    ContentItemStatusResult itemStatus = indexer.GetContentItemStatus("item1");
    if (itemStatus.Status == ContentItemStatus.Completed)
    {
        Console.WriteLine("item1 is fully indexed.");
    }

    // Items are NOT reindexed automatically. Discover flagged items and resubmit them.
    ContentItemReader reader = indexer.GetContentItemsRequiringReindexing();
    foreach (string contentId in reader)
    {
        // Rebuild the content from your own data store and resubmit it.
        IndexableAppContent content = Helpers.RebuildContentFromStore(contentId);
        indexer.AddOrUpdate(content);
    }
}
```

You can also `await indexer.WaitForIndexingIdleAsync(timeout)` to wait until indexing for the instance is idle, and subscribe to `AppContentIndexer.Listener` events (`IndexStatisticsChanged`, `ContentItemStatusChanged`, `IndexCapabilitiesChanged`) to be notified of changes.

## Remove content and delete an index

Remove individual items, several items, or everything in the index. Items are scheduled for deletion, and removing an item that doesn't exist is not an error.

```csharp
public void RemoveContentSample()
{
    using AppContentIndexer indexer = GetIndexerForApp();
    indexer.RemoveContentItem("item1");
    indexer.RemoveContentItems(new[] { "item2", "item3" });
    indexer.RemoveAllContentItems();
}
```

To enumerate or delete whole indexes, use the static APIs. `DeleteIndex` can defer deletion until all open instances are closed.

```csharp
public void DeleteIndexSample()
{
    foreach (string name in AppContentIndexer.GetExistingIndexes())
    {
        Console.WriteLine(name);
    }
    DeleteIndexResult result = AppContentIndexer.DeleteIndex("myindex", DeleteIndexWhileInUseBehavior.DeferIfInUse);
    Console.WriteLine($"Delete status: {result.Status}");
}
```

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
