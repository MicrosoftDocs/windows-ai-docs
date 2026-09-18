---
title: App Content Search Overview
description: Learn how App Content Search and the Windows AI AppContentIndexer API can enhance your Windows app search capabilities using AI to search based on semantic meaning and intent.
ms.topic: article
ms.date: 09/18/2026
---

# App Content Search Overview

The App Content Search feature enabled by the Windows AI APIs lets app developers integrate intelligent search capabilities into their Windows apps using the [AppContentIndexer](/windows/windows-app-sdk/api/winrt/microsoft.windows.search.appcontentindex.appcontentindexer) API. By indexing in-app content and making it searchable through semantic queries, users can retrieve results based not only on exact keywords but also on semantic meaning. You can use this semantic index to enhance your own AI assistants with domain-specific knowledge, creating more personalized, context-specific experiences.

> [!IMPORTANT]
> App Content Search is available in Windows App SDK 2.5.1 as a [Limited Access Feature (LAF)](https://aka.ms/laffeatures). Your app must obtain a LAF token for feature ID `com.microsoft.windows.ai.appcontentindexer` and unlock the feature at runtime before calling any `AppContentIndex` API. See [Get started with App Content Search](app-content-search-tutorial) for the request and unlock steps.

Use this API to:

- Build in-app search experiences that use both semantic and lexical search. Users can search by meaning, in addition to exact keyword matches, making it easier to find relevant information.

- Support Retrieval-Augmented Generation (RAG) by enabling local knowledge retrieval. When paired with a Large Language Model (LLM), this allows you to retrieve the most relevant content from your app's knowledge base and generate more accurate, context-aware responses.

The `AppContentIndex` APIs ship in Windows App SDK 2.5.1. The `Microsoft.WindowsAppSDK` 2.5.1 metapackage includes `Microsoft.WindowsAppSDK.Search` 2.5.5. Installing the package does not authorize an app to call the APIs; App Content Search is a Limited Access Feature and requires a token. See [Get started with App Content Search](app-content-search-tutorial) for package, manifest, device, and token setup.

> [!div class="nextstepaction"]
> [Open AI Dev Gallery to try App Content Search](aidevgallery://apis/f8465a45-8e23-4485-8c16-9909e96eacf6)

The AI Dev Gallery app offers an interactive sample of App Content Search. AI Dev Gallery demonstrates the experimental channel release of the API, which does not require a LAF token, so its samples do not include the unlock step that production apps need. [Learn more about the AI Dev Gallery](../ai-dev-gallery/index.md), including how to install it from the Microsoft Store or build it from source on GitHub.

## Requirements

| Requirement | Detail |
| --- | --- |
| Windows App SDK | 2.5.1 or later |
| Package | `Microsoft.WindowsAppSDK.Search` 2.5.5, included by `Microsoft.WindowsAppSDK` 2.5.1 |
| App model | Packaged app, or packaged with external location (package identity is required) |
| Access | LAF token for `com.microsoft.windows.ai.appcontentindexer` |
| Capability | `systemaimodels` for semantic indexing and image text recognition |
| Hardware | A supported NPU-enabled device for semantic matching. Lexical matching does not require an NPU. |

## What is the AppContentIndexer API?

The **AppContentIndexer API** indexes text and image content supplied by your app and returns relevant results for a query. It matches on keywords and, on supported NPU-enabled devices, on meaning, and returns a single fused result set. Indexing and retrieval run on the device, and each app's index is stored in its own local app data. The API chunks long text automatically and manages the text index, embedding model, and vector storage, so your app gets local semantic retrieval and retrieval-augmented generation (RAG) without building that infrastructure.

### Benefits

- **On-device indexing and retrieval** — Content is indexed and queried locally and the index is stored in the app's local app data. App Content Search does not send indexed content to a cloud search or embedding service.
- **Automatic text chunking** — Pass text of any length. App Content Search splits it into model-sized chunks for indexing and retrieval.
- **Managed search infrastructure** — App Content Search owns lexical indexing, embedding generation, vector storage, index persistence, and ranking.
- **Automatic use of available capabilities** — App Content Search detects what the device supports and uses semantic matching when it is available, without the app branching on hardware.
- **Text and image content** — Index both text and images. Queries match indexed text, text recognized inside images, and image content.
- **Persistent index** — The index is saved to disk and reopened on the next launch, so apps do not re-index content at every startup.
- **RAG-ready** — Queries return content IDs your app resolves to its own content, which it then adds as grounding context to a language model prompt.

### Developer responsibilities

App Content Search indexes the content your app supplies. It does not store your app's original content and does not watch your app's files or data store. Your app must:

- Retain the original content and metadata behind every content ID, because queries return only content IDs.
- Detect additions, edits, moves, and deletions in its own data and call the corresponding add, update, and remove operations on the index.
- Handle per-item indexing failures and re-submit or rebuild affected items.
- Call the LAF unlock API and confirm authorization before any index call.
- Choose which content to index in line with the app's own privacy commitments.

Behind the scenes, it uses advanced techniques like embedding vectors, vector databases, and traditional text indexing, but these details are fully abstracted. Developers interact with a simple, high-level API.
When content is indexed, the system stores embedding vectors (which capture semantic meaning) along with content identifiers. Search requests then return identifiers based on either keyword matches or semantic similarity. For example, searching for "kitten" might return related text about cats or images of kittens. Semantic searches work best with descriptive phrases, so a query like "cats sitting on windowsills" is more likely to produce highly relevant results.

The index is persisted to disk, so re-indexing isn't needed on each app launch.

### Semantic and lexical search

A single query returns one ranked result set that can include:

- **Lexical matches** — keyword matches in indexed text, including text recognized inside indexed images.
- **Semantic matches** — content that is close in meaning even when it shares no words with the query. Semantic matching requires a supported NPU-enabled device and the semantic index capability.

App Content Search determines which capabilities are available for the current system and index, and uses semantic matching automatically when it is available. Your app issues the same query either way and does not need a separate semantic search mode.

### Supported content types

ApplicationContentIndexer supports adding the following types of content:

- **Text** – plain or structured text content.
- **Images** – including screenshots, photos, or image files that contain text or recognizable visual elements.

### App-defined content identifiers

**AppContentIndexer** supports app-managed content by allowing apps to index items using app-defined content identifiers. Queries return these identifiers, which the app uses to retrieve the actual content from its own data store.

Text queries return AppManagedTextQueryMatch objects, and image queries return AppManagedImageQueryMatch objects—both include only the ContentId, not the content itself.

For guidance on how to integrate this feature into your app and use the ApplicationContentIndexer API, see: [Quickstart: App Content Search](app-content-search-tutorial.md)

## Privacy and security

App Content Search generates lexical and semantic indexes locally and stores them in your app's local app data. Indexed content is not sent to a cloud search or embedding service.

Index contents are protected by the same boundary as the rest of your app's local app data: the signed-in Windows user account and the device it is stored on. App Content Search does not add a separate encryption or authentication layer on top of that boundary. Apps can index personal or business content when that protection level matches their requirements. If your app must protect content beyond the signed-in user and local app data boundary, apply your own safeguards and decide accordingly which content to index.

## Responsible AI considerations

The semantic indexing and search capabilities in App Content Search do not apply content moderation and do not detect or mitigate semantic bias in the underlying models. Developers are responsible for evaluating and managing these risks when they implement AI-powered features.

We recommend reviewing the [Responsible Generative AI Development on Windows guidelines](/windows/ai/rai) for best practices when building AI experiences in your app.
