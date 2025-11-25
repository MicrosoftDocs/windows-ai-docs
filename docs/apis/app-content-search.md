---
title: App Content Search Overview
description: Learn how App Content Search and the Windows AI AppContentIndexer API can enhance your Windows app search capabilities using AI to search based on semantic meaning and intent.
ms.topic: article
ms.date: 11/17/2025
---

# App Content Search Overview

The App Content Search feature enabled by the Windows AI APIs lets app developers integrate intelligent search capabilities into their Windows apps using the [AppContentIndexer](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.search.experimental.appcontentindex.appcontentindexer) API. By indexing in-app content and making it searchable through semantic queries, users can retrieve results based not only on exact keywords but also on semantic meaning. You can use this semantic index to enhance your own AI assistants with domain-specific knowledge, creating more personalized, context-specific experiences.

Use this API to:

- Build in-app search experiences that use both semantic and lexical search. Users can search by meaning, in addition to exact keyword matches, making it easier to find relevant information.

- Support Retrieval-Augmented Generation (RAG) by enabling local knowledge retrieval. When paired with a Large Language Model (LLM), this allows you to retrieve the most relevant content from your app's knowledge base and generate more accurate, context-aware responses.

The ApplicationContentIndexer API is currently only available in Windows App SDK release 2.0 Experimental 2.

> [!div class="nextstepaction"]
> [Open AI Dev Gallery to try App Content Search](aidevgallery://apis/F8465A45-8E23-4485-8C16-9909E96EACF6)

The AI Dev Gallery app offers an interactive sample of the AppContentIndexer API enabling you to experiment with the App Content Search feature. [Learn more about the AI Dev Gallery](../ai-dev-gallery/index.md), including how to install from the Microsoft Store or from the source code on GitHub.

## What is the AppContentIndexer API?

The **AppContentIndexer API** allows apps to make their text and image content searchable using both keyword-based (lexical) and meaning-based (semantic) search—without requiring developers to understand the underlying complexity.

Behind the scenes, it uses advanced techniques like embedding vectors, vector databases, and traditional text indexing, but these details are fully abstracted. Developers interact with a simple, high-level API.
When content is indexed, the system stores embedding vectors (which capture semantic meaning) along with content identifiers. Search requests then return identifiers based on either keyword matches or semantic similarity. For example, searching for "kitten" might return related text about cats or images of kittens. Semantic searches work best with descriptive phrases, so a query like "cats sitting on windowsills" is more likely to produce highly relevant results.

The index is persisted to disk, so re-indexing isn't needed on each app launch.

### Semantic and lexical search

Internally, ApplicationContentIndexer uses a combination of traditional text indexing and modern vector-based search powered by embeddings. These details are abstracted away – developers do not need to manage embedding models, vector storage, or retrieval infrastructure directly.

You can query the index using a plain string. The query may return:

- **Lexical matches** – exact text matches (including text found within images).
- **Semantic matches** – content that is similar in meaning, even if the words are not identical.

For example, a query for "kitten" might return a reference to:

- Text entries about cats, even if the word "kitten" isn't explicitly mentioned.
- Images that visually contain kittens.
- Textual content in images that contain 'cat' or words with enough semantic relevance.

### Supported content types

ApplicationContentIndexer supports adding the following types of content:

- **Text** – plain or structured text content.
- **Images** – including screenshots, photos, or image files that contain text or recognizable visual elements.

### App-defined content identifiers

**AppContentIndexer** supports app-managed content by allowing apps to index items using app-defined content identifiers. Queries return these identifiers, which the app uses to retrieve the actual content from its own data store.

Text queries return AppManagedTextQueryMatch objects, and image queries return AppManagedImageQueryMatch objects—both include only the ContentId, not the content itself.

For guidance on how to integrate this feature into your app and use the ApplicationContentIndexer API, see: [Quickstart: App Content Search](app-content-search-tutorial.md)

## Privacy and security

Semantic and lexical indexes are generated on behalf of your app and stored in the app's local app data folder. As part of the private preview release, this feature is intended for indexing non-sensitive application content. For best security practices, do not use this feature to index user data that may contain personal, confidential, or sensitive information.

## Responsible AI considerations

The semantic indexing and search capabilities in this preview do not apply any form of content moderation, nor do they attempt to detect or mitigate semantic bias introduced by the underlying models. Developers are responsible for evaluating and managing the potential risks when implementing AI-powered features.

We recommend reviewing the [Responsible Generative AI Development on Windows guidelines](/windows/ai/rai) for best practices when building AI experiences in your app.
