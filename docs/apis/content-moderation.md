---
title: Content safety moderation with Windows Copilot Runtime
description: Learn how Windows Copilot Runtime moderates content and how to adjust sensitivity filters.
ms.topic: article
ms.date: 04/14/2025
dev_langs:
- csharp
- cpp
---

# Content safety moderation with Windows Copilot Runtime

Windows Copilot Runtime APIs, such as [Phi Silica API](phi-silica-api-ref.md) or [Imaging API](imaging-api-ref.md), implement text Content Moderation to classify and filter out potentially harmful content from user prompts or in responses returned by these generative models. By default, these API will filter out content classified as potentially harmful, but sensitivity levels can be configured.

For **API details**, see [API ref for Content Safety Moderation](content-moderation-api-ref.md).

## Prerequisites

Complete the steps in [Get started building an app with Windows Copilot Runtime APIs](get-started.md).

## Text content moderation

You can adjust content moderation on the input prompt to the generative model and the AI generated output. The Windows Copilot Runtime APIs content moderation is designed and implemented similarly to the one provided by [Azure AI Content Safety](/azure/ai-services/content-safety/overview).

## Harm categories

Harm categories align with the definitions used in Azure AI Content Safety and can be found in the [Azure AI Content Safety guidance](/azure/ai-services/content-safety/concepts/harm-categories). Harm categories include: Hate and Fairness, Sexual content, Violence, or Self-Harm, and can include multiple labels on the same content.

These four categories classifying potentially harmful content enable sensitivity filters to be adjusted.

| Category | Description | API name |
| -------- | ----------- | -------- |
| Hate     | Hate and fairness harms refer to any content that attacks or uses discriminatory language with reference to a person or identity group based on certain differentiating attributes of these groups. | `HateContentSeverity` |
| Sexual   | Sexual describes language related to anatomical organs and genitals, romantic relationships and sexual acts, acts portrayed in erotic or affectionate terms, including those portrayed as an assault or a forced sexual violent act against one's will. | `SexualContentSeverity` |
| Violence | Violence describes language related to physical actions intended to hurt, injure, damage, or kill someone or something; describes weapons, guns, and related entities. | `ViolentContentSeverity` |
| Self Harm | Self-harm describes language related to physical actions intended to purposely hurt, injure, damage one's body or kill oneself. | `SelfHarmContentSeverity` |

## Severity levels

By default, all calls to Windows Copilot Runtime generative APIs use content moderation, but the severity level can be adjusted.

- `high`: Not available. Content classified as severity level 3+ (high-risk for potential harm) is currently blocked from being returned by the generative AI model.

- `medium`: The default severity level is set to `medium`. Content classified as severity level 0 - 3 will be returned.

- `low`: Lowers the risk of returning potentially harmful content further. Only content classified as severity level 0 - 1 will be returned.

To learn more about severity levels, see [Azure AI Content Safety Harm Categories](/azure/ai-services/content-safety/concepts/harm-categories).

## Text Content Moderation code sample

To configure Text Content Moderation severity filters embedded within Windows Copilot Runtime, you will need to pass the `ContentFilterOptions` struct as a parameter to the API used for response generation, such as the [Phi Silica API](./phi-silica.md).

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

## See also

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for Phi Silica in the Windows App SDK](phi-silica-api-ref.md)
- [API ref for AI imaging in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows Copilot Runtime Sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsCopilotRuntime)
