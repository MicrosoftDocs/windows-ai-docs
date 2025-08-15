---
title: Deploying your app that uses Windows ML
description: Learn how to deploy your app that uses Windows Machine Learning (ML).
ms.date: 07/07/2025
ms.topic: concept-article
---

# Deploying your app that uses Windows ML

When you're ready to distribute your C# or C++ app that uses Windows ML, you need to ensure that the Windows App SDK framework is properly deployed to your users' devices. The Windows ML runtime is distributed as part of the Windows App SDK.

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

## Supported deployment options for Windows ML

Windows ML currently requires the use of the framework-dependent deployment option in Windows App SDK. See the [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview) for more details about the deployment options in Windows App SDK.

### Framework-dependent: ✅ Supported

Your app depends on the Windows App SDK runtime and/or framework package being present on the target machine. Framework-dependent deployment is the default deployment mode of the Windows App SDK for its efficient use of machine resources and serviceability. See [Deployment architecture and overview for framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for more details.

### Self-contained: ❌ Not supported

Use of the self-contained deployment option in Windows App SDK is currently not supported when using Windows ML.

## Additional resources

For more detailed information on deploying Windows App SDK applications, refer to these resources:

* [Get started with the Windows App SDK](/windows/apps/windows-app-sdk/set-up-your-development-environment)
* [Windows App SDK deployment guide](/windows/apps/package-and-deploy/deploy-overview)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)