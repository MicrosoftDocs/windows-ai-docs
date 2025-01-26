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

When using any of the AI APIs from the Windows App SDK, the model must be available for use before 

## Prerequisites

- [CoPilot+ PC](/windows/ai/npu-devices/) (AMD-based CoPilot+ PCs do not currently support Image Super Resolution).

## What can I do with Image Super Resolution?

- Call IsAvailable() before every call to check if the model is on the user's device.
- If it's not, call MakeAvailable(). This will run in the background and the user can see the download in Settings -> Windows Update
- MakeAvailable() has a status option which you can use to show a loading UI if you wish
- If the user has unsupported hardware, MakeAvailable() will fail with error xyz. 

```csharp
using Microsoft.Windows.Imaging;
using Microsoft.Windows.Management.Deployment;
using Windows.Graphics.Imaging;

if (!ImageScaler.IsAvailable())
{
    var result = await ImageScaler.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}
ImageScaler imageScaler = await ImageScaler.CreateAsync();
SoftwareBitmap finalImage = imageScaler.ScaleSoftwareBitmap(softwareBitmap, targetWidth, targetHeight);
```

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
