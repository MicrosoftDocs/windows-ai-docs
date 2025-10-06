---
title: Windows API troubleshooting and FAQ
description: Troubleshooting and frequently asked questions for Windows AI APIs
ms.date: 10/03/2025
ms.topic: overview
no-loc: [Windows AI Foundry, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
---

# Windows AI API troubleshooting and FAQ

> [!IMPORTANT]
> Apps using the Phi Silica APIs might encounter issues with Limited Access Feature support (see [LimitedAccessFeatures class](/uwp/api/windows.applicationmodel.limitedaccessfeatures)).
>
> At this time, we reccommend using [experimental releases](/windows/apps/windows-app-sdk/experimental-channel) as they do not require LAF tokens.

- Apps that use Windows AI APIs need to be granted package identity at runtime. For details, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app). If you are seeing an UnauthorizedAccessException error (or having other access issues), ensure that your app is packaged and the systemAIModels capability was added to your manifest file (see [Get started building an app with Windows AI APIs](./get-started.md)).

- It's not possible to run a self-contained app from the **Downloads** folder (or from anywhere under the `C:\Users` folder). For more details, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app) and [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview).

- If you are having trouble, first try running an API on your Copilot+ PC using the [AI Dev Gallery app](../ai-dev-gallery/index.md). If this fails, verify that you have the required models installed on your machine by going to **System > AI Components** in Windows Settings. Entries for each AI model will be listed. If the required AI model is not listed, check to ensure that you have the correct branch selected.

- The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

- Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Phi Silica** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
