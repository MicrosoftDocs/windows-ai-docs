---
title: Deploy your app that uses Windows ML
description: Learn how to deploy your app that uses Windows Machine Learning (ML).
ms.date: 02/05/2026
ms.topic: concept-article
---

# Deploy your app that uses Windows ML

When you're ready to distribute your app that uses Windows ML, you need to ensure that the runtime is properly deployed to your users' devices. Windows ML supports both Windows App SDK and self-contained deployment models.

## Deployment options with the Windows App SDK

If your app uses the Windows App SDK, Windows ML supports both the framework-dependent and the self-contained deployment options. See the [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview) for more details.

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

## Self-contained deployment (without Windows App SDK): ✅ Supported

For native C/C++ middleware, game engines, and cross-platform applications, Windows ML can be consumed via [vcpkg](./native-integration.md) or the `Microsoft.WindowsAppSDK.ML` NuGet package without any Windows App SDK dependency. Runtime DLLs are bundled alongside your application or library.

```
MyApp/
├── MyApp.exe (or MyPlugin.dll)
├── Microsoft.Windows.AI.MachineLearning.dll
├── onnxruntime.dll
├── onnxruntime_providers_shared.dll
└── DirectML.dll
```

With self-contained deployment:

- No Windows App SDK framework installation is required on the target machine.
- You manage runtime updates through vcpkg or NuGet package updates.
- EP discovery and download is handled by the Windows ML C API (`WinMLEpCatalog.h`).

For setup instructions, see [Use Windows ML without Windows App SDK](./native-integration.md).

> [!NOTE]
> If your application already bootstraps the Windows App SDK, vcpkg and NuGet redist consumers can also use the framework package's runtime DLLs without bundling them locally. For details, see [Using with Windows App SDK framework-dependent apps](./native-integration.md#using-with-windows-app-sdk-framework-dependent-apps).

## Additional resources

For more detailed information on deploying Windows App SDK applications, refer to these resources:

* [Get started with the Windows App SDK](/windows/apps/windows-app-sdk/set-up-your-development-environment)
* [Windows App SDK deployment guide](/windows/apps/package-and-deploy/deploy-overview)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML)