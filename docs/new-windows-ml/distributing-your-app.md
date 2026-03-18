---
title: Deploy your app that uses Windows ML
description: Learn how to deploy your app that uses Windows Machine Learning (ML).
ms.date: 03/10/2026
ms.topic: concept-article
---

# Deploy your app that uses Windows ML

Windows ML is deployed like any other Windows App SDK component, supporting both framework-dependent and self-contained deployment. See the [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview) for more details about the deployment options in Windows App SDK.

## Framework-dependent

Your app depends on the Windows App SDK runtime and/or framework package being present on the target machine. Framework-dependent deployment is the default deployment mode of the Windows App SDK for its efficient use of machine resources and serviceability. See [Deployment architecture and overview for framework-dependent apps](/windows/apps/windows-app-sdk/deployment-architecture) for more details.

## Self-contained

Your app carries the Windows App SDK dependencies with it. See [Deployment guide for self-contained apps](/windows/apps/package-and-deploy/self-contained-deploy/deploy-self-contained-apps) for more details.

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