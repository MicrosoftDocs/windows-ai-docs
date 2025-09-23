---
title: Deploying your app that uses Windows ML
description: Learn how to deploy your app that uses Windows Machine Learning (ML).
ms.date: 07/07/2025
ms.topic: concept-article
---

# Deploying your app that uses Windows ML

When you're ready to distribute your C# or C++ app that uses Windows ML, you need to ensure that the Windows App SDK framework is properly deployed to your users' devices. The Windows ML runtime is distributed as part of the Windows App SDK.

## Supported deployment options for Windows ML

Windows ML supports both the framework-dependent and the self-contained deployment options in Windows App SDK. See the [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview) for more details about the deployment options in Windows App SDK.

### Framework-dependent: ✅ Supported

Your app depends on the Windows App SDK runtime and/or framework package being present on the target machine. Framework-dependent deployment is the default deployment mode of the Windows App SDK for its efficient use of machine resources and serviceability. See [Deployment architecture and overview for framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for more details.

### Self-contained: ✅ Supported

With the GA release of Windows ML, use of the self-contained deployment option in Windows App SDK is now supported when using Windows ML. See [Deployment guide for self-contained apps](/windows/apps/package-and-deploy/self-contained-deploy/deploy-self-contained-apps) for more details.

In self-contained mode, ONNX Runtime binaries are deployed alongside your application:

```
MyApp/
├── MyApp.exe
├── Microsoft.Windows.AI.MachineLearning.dll
├── onnxruntime.dll
├── onnxruntime_providers_shared.dll
└── DirectML.dll
```

## Additional resources

For more detailed information on deploying Windows App SDK applications, refer to these resources:

* [Get started with the Windows App SDK](/windows/apps/windows-app-sdk/set-up-your-development-environment)
* [Windows App SDK deployment guide](/windows/apps/package-and-deploy/deploy-overview)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)