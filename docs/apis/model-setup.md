---
title: Get started with the basics of AI in the Windows App SDK
description: Learn how to setup 
ms.topic: article
ms.date: 01/25/2025
ms.author: emhuang
author: emhuang-msft
dev_langs:
- csharp
- cpp
---

# Get started with the basics of AI in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

Before diving into the APIs, it is important to make it a habit to check for model availability. Unlike typical Windows App SDK APIs where a developer can call on an API to immediately provide functionality or content, the AI APIs rely on the model to be available before providing anything back to the developer. 

Because of this, it is important to keep in mind of whether the model of choice is available on a user's device when building features with these APIs. 

## Prerequisites

- [CoPilot+ PC](/windows/ai/npu-devices/)

## Checking Model Availability 

The process of checking model availability is simple to understand and has few steps, starting with first calling ```IsAvailable()``` to check if the model is on the user's device. The method returns ```true``` if model being called is installed. It's important to note that this method needs to be called before every call to the model. 

If it's the model isn't available on the user's device, the developer can call ```MakeAvailableAsync()``` to start the model's installation. The installation will run in the background, and the user will be able to check on the download progress in the Windows Update page of the Settings application. Something to note about ``MakeAvailableAsync()`` is that the method has a status option which can be used to show a loading UI. If the user has unsupported hardware, ```MakeAvailableAsync()``` will fail with error **CHANGE ERROR HERE**. 

If the model is available, the developer can call ```CreateAsync()``` to create a new instance from a class that belongs to the model. From there, they can use the APIs that belong to that class for their application.

Here are the APIs in practice:

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
