---
title: Distributing your app that uses Windows ML
description: Learn how to distribute your app that uses Windows Machine Learning (ML).
ms.date: 07/07/2025
ms.topic: concept-article
---

# Distributing your app that uses Windows ML

When you're ready to distribute your C# or C++ app that uses Windows ML, you need to ensure that the Windows App SDK framework is properly deployed to your users' devices. The Windows ML runtime is distributed as part of the Windows App SDK.

> [!IMPORTANT]
> The Windows ML APIs are currently experimental and **not supported** for use in production environments. Apps trying out these APIs should not be published to the Microsoft Store.

## Framework-Dependent deployment

Windows ML is delivered as a _framework-dependent_ component through the Windows App SDK. This means your application must include the proper references to ensure the Windows App SDK runtime is deployed.

### For packaged applications (MSIX)

For MSIX packaged applications, you need to include a dependency on the Windows App SDK framework package in your application manifest.

For more details, see [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview).

### For unpackaged applications

For unpackaged (non-MSIX) applications, Windows ML relies on the Windows App SDK bootstrapper to initialize the necessary components. Add the following property to your project file:

```xml
<PropertyGroup>
  <WindowsPackageType>None</WindowsPackageType>
</PropertyGroup>
```

The Windows App SDK bootstrapper will handle initializing the framework at runtime. Your application installer is responsible for deploying a compatible version of the Windows App SDK. See the [Windows App SDK deployment guide](/windows/apps/package-and-deploy/deploy-overview) for more details.

### NuGet package references

You must include one of the following NuGet package references:

1. **Reference the main Windows App SDK package (recommended)**
   ```xml
   <PackageReference Include="Microsoft.WindowsAppSDK"/>
   ```
   This will automatically include `Microsoft.WindowsAppSDK.ML` as a transitive dependency.

2. **Or, reference both ML and WindowsAppSDK Runtime directly**
   ```xml
   <PackageReference Include="Microsoft.WindowsAppSDK.ML"/>
   <PackageReference Include="Microsoft.WindowsAppSDK.Runtime"/>
   ```

> [!IMPORTANT]
> If you only reference `Microsoft.WindowsAppSDK.ML` without `Microsoft.WindowsAppSDK.Runtime`, your app will not run correctly.

### Python requirements.txt

```
--index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple
--extra-index-url https://pypi.org/simple
onnxruntime-winml==1.22.0.post2
winrt-runtime==3.2.1
winrt-Windows.Foundation==3.2.1
winrt-Windows.Foundation.Collections==3.2.1
winui3-Microsoft.Windows.AI.MachineLearning==1!1.8.250702007.dev4
winui3-Microsoft.Windows.ApplicationModel.DynamicDependency.Bootstrap==1!1.8.250702007.dev4
```

## Windows App SDK bootstrapper

The Windows App SDK bootstrapper handles the initialization and loading of the Windows App SDK framework components, including Windows ML. The bootstrapper is automatically included when you reference the Windows App SDK NuGet packages via the combination of the Microsoft.WindowsAppSDK.Foundation and Microsoft.WindowsAppSDK.Runtime packages.

---

## Additional resources

For more detailed information on deploying Windows App SDK applications, refer to these resources:

* [Get started with the Windows App SDK](/windows/apps/windows-app-sdk/set-up-your-development-environment)
* [Windows App SDK deployment guide](/windows/apps/package-and-deploy/deploy-overview)
* [Windows ML samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)

---
