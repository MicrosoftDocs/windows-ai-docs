---
title: Check for AI model availability with the Windows App SDK
description: Learn how to setup 
ms.topic: article
ms.date: 02/06/2025
ms.author: mattwoj
author: mattwojo
reviewer: raamleka
dev_langs:
- csharp
- cpp
---

# Check for model availability before implementing an AI feature

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

When implementing an AI feature using Windows Copilot Runtime APIs, the app should first check for the availability of the AI model supporting that feature. Unlike typical Windows App SDK APIs where a developer can call on an API to immediately provide functionality or content, the Windows Copilot Runtime APIs rely on the model to be available on the app users machine.

## Checking Model Availability 

To check if the model required by an AI feature is available on the user's device, begin by calling: `IsAvailable()`. This method will return `true` if the model being called is installed on the user's device. This method needs to be called before every call to the model.

If the model is not available on the user's device, the method `MakeAvailableAsync()` can be called to install the required model. The model installation will run in the background, and the user will be able to check on the install progress in the Windows Update page of the Settings application.

The `MakeAvailableAsync()` method has a status option which can show a loading UI. If the user has unsupported hardware, `MakeAvailableAsync()` will fail with an error.

Once the model is available, `CreateAsync()` can be called to create a new instance from a class that belongs to the model. The APIs that belong to that class can then be used in the app.

## Code sample

The following sample demonstrates checking for model availability.

```csharp
using Microsoft.Windows.AI.Generative; 
 
 
if (!LanguageModel.IsAvailable()) 
{ 
   var op = await LanguageModel.MakeAvailableAsync(); 
} 
 
using LanguageModel languageModel = await LanguageModel.CreateAsync();

string prompt = "Provide the molecular formula for glucose.";  
var result = await languageModel.GenerateResponseAsync(prompt); 
 
Console.WriteLine(result.Response); 
```

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
