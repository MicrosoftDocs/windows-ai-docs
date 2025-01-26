---
title: Content safety with generative AI APIs 
description: Learn about the content moderation you can add to generative AI APIs that are shipping in Windows App SDK.
ms.topic: article
ms.date: 12/19/2024
ms.author: anarvekar
author: anarvekar-msft
dev_langs:
- csharp
- cpp
---

# Content safety with generative AI APIs 

> [!IMPORTANT]
> **This feature is not yet available.** It is expected to ship in an upcoming [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.

Several generative AI models are made available through the Windows Copilot Runtime APIs. To ensure that harmful content is not prompted to or produced by these generative models, the generative APIs make use of content moderation.

The content moderation calls are embedded in the generative APIs and allow you to classify and filter harmful content. By default, all generative APIs have content moderation enabled. However, you can experiment with different sensitivity levels to configure your content filters. 

## Where its used 

* [Phi silica API](phi-silica-api-ref.md)


## Prerequisites 

CoPilot+ PCs containing a Qualcomm chip 

 
## Text content moderation 

You can adjust content moderation on the input prompt to the generative model and the AI generated output. The Windows Copilot Runtime APIs content moderation is designed and implemented similarly to the one provided by [Azure AI Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview).


### Harm categories

There are four categories of objectionable content you can choose to classify and filter with the text content moderation API

| Category | Description | API name |
| -------- | ----------- | -------- |
| Hate     | Hate and fairness harms refer to any content that attacks or uses discriminatory language with reference to a person or identity group based on certain differentiating attributes of these groups. | ```HateContentSeverity``` |
| Sexual   | Sexual describes language related to anatomical organs and genitals, romantic relationships and sexual acts, acts portrayed in erotic or affectionate terms, including those portrayed as an assault or a forced sexual violent act against one’s will. | ```SexualContentSeverity``` |
| Violence | Violence describes language related to physical actions intended to hurt, injure, damage, or kill someone or something; describes weapons, guns, and related entities. | ```ViolentContentSeverity``` |
| Self Harm | Self-harm describes language related to physical actions intended to purposely hurt, injure, damage one’s body or kill oneself. | ```SelfHarmContentSeverity``` |


### Severity levels

Every harm category comes with a severity level rating. The severity level is meant to indicate the severity of the flagged content. You have the option to select from three severity levels: ```Safe```, ```Low```, ```Medium```. By default, all calls to Windows Copilot Runtime generative APIs use content moderation, but you have the option to customize the severity level. 

A safe severity level applies the highest content moderation. These severity levels correspond to those of Azure AI content safety:

* [0, 1] -> ```safe```
* [2, 3] -> ```low```
* [4, 5] -> ```medium```

To learn more what these severity levels mean, see [Azure AI Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/harm-categories?tabs=warning). 

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
