---
title: Content safety moderation with Windows AI Foundry
description: Learn how Windows AI Foundry moderates content and how to adjust sensitivity filters.
ms.topic: article
ms.date: 09/17/2025
dev_langs:
- csharp
- cpp
---

# Content safety moderation with Windows AI Foundry

Windows AI APIs, such as [Phi Silica](phi-silica.md) and [Imaging](imaging.md), use content moderation to classify and filter out potentially harmful content from user prompts or in responses returned by the generative models. By default, these API filter out content classified as potentially harmful, but sensitivity levels can be configured.

For **API details**, see [API ref for content safety moderation](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety).

## Prerequisites

Complete the steps in [Get started building an app with Windows AI APIs](get-started.md).

## Adjust content safety moderation

You can adjust content moderation on the input prompt to the generative model and the AI generated output. The Windows AI APIs content moderation is designed and implemented similarly to the one provided by [Azure AI Content Safety](/azure/ai-services/content-safety/overview).

### Harm categories

The harm categories supported by Windows AI APIs align with those defined by [Azure AI Content Safety](/azure/ai-services/content-safety/concepts/harm-categories). Harm categories include *Hate and fairness*, *Sexual*, *Violence*, and *Self-harm* (multiple categories can be assigned to the same content).

| Category | Description | API name |
| -------- | ----------- | -------- |
| Hate     | Content that attacks or uses discriminatory language with reference to a person or identity group based on certain differentiating attributes of these groups. | `HateContentSeverity` |
| Sexual | Content related to anatomical organs and genitals, romantic relationships and sexual acts, acts portrayed in erotic or affectionate terms, including those portrayed as an assault or a forced sexual violent act against one's will. | `SexualContentSeverity` |
| Violence | Content related to physical actions intended to hurt, injure, damage, or kill someone or something; describes weapons, guns, and related entities. | `ViolentContentSeverity` |
| Self harm | Content related to physical actions intended to purposely hurt, injure, damage one's body or kill oneself. | `SelfHarmContentSeverity` |

### Severity levels

By default, all calls to Windows AI Foundry generative APIs use content moderation, but the severity level can be adjusted.

- `high`: Not available. Content classified as severity level 3+ (high-risk for potential harm) is currently blocked from being returned by the generative AI model.

- `medium`: The default severity level is set to `medium`. Content classified as severity level 0 - 3 will be returned.

- `low`: Further lowers the risk of returning potentially harmful content. Only content classified as severity level 0 - 1 will be returned.

To learn more about severity levels, see [Azure AI Content Safety Harm Categories](/azure/ai-services/content-safety/concepts/harm-categories).

## Text Content Moderation code sample

To configure Text Content Moderation severity filters embedded within Windows AI Foundry, you must pass the [**ContentFilterOptions**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.contentsafety.contentfilteroptions) struct as a parameter to the API used for response generation (such as the [Phi Silica API](./phi-silica.md)).

The following code sample demonstrates adding Text Content Moderation severity filters to the Microsoft Windows Generative AI [**LanguageModel**](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel):

```csharp
LanguageModelOptions options = new LanguageModelOptions();
ContentFilterOptions filterOptions = new ContentFilterOptions();

// prompt
filterOptions.PromptMaxAllowedSeverityLevel.Violent = SeverityLevel.Minimum;
filterOptions.PromptMaxAllowedSeverityLevel.Hate = SeverityLevel.Low;
filterOptions.PromptMaxAllowedSeverityLevel.SelfHarm = SeverityLevel.Medium;
filterOptions.PromptMaxAllowedSeverityLevel.Sexual = SeverityLevel.High;

//response
filterOptions.ResponseMaxAllowedSeverityLevel.Violent = SeverityLevel.Medium;

//image
filterOptions.ImageMaxAllowedSeverityLevel.AdultContentLevel = SeverityLevel.Medium;
filterOptions.ImageMaxAllowedSeverityLevel.RacyContentLevel = SeverityLevel.Medium;

options.ContentFilterOptions = filterOptions;

var result = await languageModel.GenerateResponseAsync(prompt, options);

Console.WriteLine(result.Text);
```

## See also

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for Phi Silica in the Windows App SDK](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai)
- [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
