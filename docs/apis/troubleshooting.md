---
title: Windows API troubleshooting and FAQ
description: Troubleshooting and frequently asked questions for Windows AI APIs
ms.date: 05/20/2025
ms.topic: overview
no-loc: [Windows AI Foundry, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
---

# Windows API troubleshooting and FAQ

> [!IMPORTANT]
> In the future, an app that uses Windows AI APIs will need to be granted package identity at runtime. For details of how to grant that, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app).

- Currently, it's not possible to run a self-contained app from the **Downloads** folder, or from anywhere under the `C:\Users` folder. For info about those terms, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app) and [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview)

- If you are having trouble, first try running an API on your Copilot+ PC using the [AI Dev Gallery app](../ai-dev-gallery/index.md). If this fails, verify that you have the required models installed on your machine by going to **System > AI Components** in the Settings app. Entries for each AI model will be listed. If the required AI model is not listed, check to ensure that you have the correct branch selected.

- The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

- Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Phi Silica** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI Foundry Sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsCopilotRuntime)
