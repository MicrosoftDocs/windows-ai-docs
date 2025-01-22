---
title: Content Moderation with Windows Copilot Runtime
description: Learn how Windows Copilot Runtime moderates content and how to adjust sensitivity filters.
ms.author: mattwoj
author: mattwojo
ms.date: 01/21/2025
ms.topic: overview
no-loc: [Windows Copilot Runtime, Phi Silica]
---

# Content Moderation with Windows Copilot Runtime

Windows Copilot Runtime implements a Text Content Moderation API to flag and filter out potentially harmful content. The API will filter out content classified as potentially harmful by default, however, developers can do some configuration to apply different sensitivity levels.

Harm categories align with the definitions used in Azure AI Content Safety and can be found in the [Azure AI Content Safety guidance](/azure/ai-services/content-safety/concepts/harm-categories). Harm categories include: Hate and Fairness, Sexual content, Violence, or Self-Harm, and can include multiple labels on the same content.

Severity levels can be assigned to each harm category, with these level settings:

- `high`: Not available. Content classified as severity level 3+ (high-risk for potential harm) is currently blocked from being returned by the generative AI model.
- `medium`: The default severity level is set to `medium`. Content classified as severity level 0 - 3 will be returned.
- `low`: Lowers the risk of returning potentially harmful content further. Only content classified as severity level 0 - 1 will be returned.

## Embedded Text Content Moderation Code Sample

To configure Text Content Moderation severity filters embedded within Windows Copilot Runtime, you will need to pass the `ContentFilterOptions` struct as a parameter to the [Phi Silica APIs](./apis/phi-silica.md) used for response generation.

The following code sample demonstrates adding Text Content Moderation severity filters to the Microsoft Windows Generative AI `LanguageModel`:

```csharp
var languageModelOptions = new LanguageModelOptions {
    Temp =  0.9f,
    Top_p = 0.9f, 
    Top_k = 40
};

var promptMinSeverityLevelToBlock = new TextContentFilterSeverity {
    HateContentSeverity = SeverityLevel.Low,
    SexualContentSeverity = SeverityLevel.Low,
    ViolentContentSeverity = SeverityLevel.Medium,
    SelfHarmContentSeverity = SeverityLevel.Low
};

var responseMinSeverityLevelToBlock = new TextContentFilterSeverity {
    HateContentSeverity = SeverityLevel.Low,
    SexualContentSeverity = SeverityLevel.Low,
    ViolentContentSeverity = SeverityLevel.Low,
    SelfHarmContentSeverity = SeverityLevel.Medium
};

var contentFilterOptions = new ContentFilterOptions {
    PromptMinSeverityLevelToBlock = promptMinSeverityLevelToBlock,
    ResponseMinSeverityLevelToBlock = responseMinSeverityLevelToBlock
};

IProgress<string> progress;
var languageModelResponseWithProgress = model.GenerateResponseWithProgressAsync(languageModelOptions, prompt, contentFilterOptions);
languageModelRepsonseWithProgress.Progress = (_, generationProgress) =>
{
    progress.Report(generationProgress);
};
string response = (await languageModelResponseWithProgress).Response;
```
