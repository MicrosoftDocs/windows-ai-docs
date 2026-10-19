---
title: Watermarking in Windows AI text APIs
description: Learn how invisible watermarks in Windows AI text API responses affect text rendering, structured output, parsing, and encoding.
author: GrantMeStrength
ms.author: jken
ms.topic: concept-article
ms.date: 10/16/2026
---

# Watermarking in Windows AI text APIs

Starting in October 2026, output from some APIs in the [Microsoft.Windows.AI.Text](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text) namespace includes an invisible watermark to indicate that the text is AI-generated. If your app processes generated text, account for the watermark when you render, parse, or convert the response.

> [!WARNING]
> You are responsible for ensuring that your app satisfies the requirements for marking AI-generated content that apply to it. Consider these requirements before removing the automatically inserted watermark.

For supported Windows versions, hardware requirements, and setup instructions for these APIs, see [Get started building an app with Windows AI APIs](get-started.md) and [Supported hardware for Phi Silica](phi-silica.md#supported-hardware).

## APIs that automatically include a watermark

The following APIs in the Microsoft.Windows.AI.Text namespace automatically include a watermark to indicate that the text is AI-generated:

- [LanguageModel.GenerateResponseAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.generateresponseasync)
- [LanguageModel.GenerateResponseFromEmbeddingsAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel.generateresponsefromembeddingsasync)
- [TextRewriter.RewriteAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textrewriter.rewriteasync)
- [TextRewriter.RewriteCustomAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textrewriter.rewritecustomasync)
- [TextSummarizer.SummarizeAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer.summarizeasync)
- [TextSummarizer.SummarizeConversationAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer.summarizeconversationasync)
- [TextSummarizer.SummarizeParagraphAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.textsummarizer.summarizeparagraphasync)

## Watermarking scheme

The watermark is the sequence of four Unicode code points `U+200B U+034F U+2060 U+200B`, in this order:

| Position | Code point | Unicode name |
| --- | --- | --- |
| 0 | U+200B | ZERO WIDTH SPACE |
| 1 | U+034F | COMBINING GRAPHEME JOINER |
| 2 | U+2060 | WORD JOINER |
| 3 | U+200B | ZERO WIDTH SPACE |

The APIs use heuristics to decide whether and where to insert the watermark. A response can contain zero, one, or multiple copies of the sequence. The heuristics avoid inserting the watermark in places where it would affect word breaking.

Watermark locations can vary between responses. The heuristics also change over time, so don't rely on a fixed watermark location or assume that every response contains the sequence.

## Handle watermarked output

### Text rendering and interaction

Most text rendering doesn't display these code points. However, they can affect text selection and cursor movement.

### Structured and formatted output

The watermark can cause issues when your app relies on structured or formatted output from the affected APIs. Examples include:

- Using Markdown for formatting.
- Generating code or markup, such as HTML or CSS.
- Generating JSON and then parsing it.

Make sure your parsing logic can handle the watermark sequence wherever it occurs in a response, including responses with multiple copies. Consider your app's requirements for marking AI-generated content before removing the watermark.

### Character encoding

Most non-Unicode code pages can't represent the watermark. Depending on the conversion API you use, converting a response to one of these code pages can produce errors, fallback characters, or other unexpected characters. Account for this behavior when your app converts or exports generated text.

## Related content

- [Get started with Phi Silica](phi-silica.md)
- [Generate structured JSON output with Phi Silica](phi-silica-structured-output.md)
- [Responsible generative AI development on Windows](../rai.md)
