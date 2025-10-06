---
title: Windows ML APIs
description: Learn about the APIs behind Windows Machine Learning (ML) which help your Windows apps run AI models locally.
ms.date: 09/26/2025
ms.topic: article
---

# Windows ML APIs

For conceptual guidance, see [Run ONNX models with Windows ML)](./run-onnx-models.md).

You can think of the APIs in the *Microsoft.WindowsAppSDK.ML* NuGet package as being the superset of these two sets:

* *Windows ML APIs*. Windows ML APIs in the **[Microsoft.Windows.AI.MachineLearning](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning)** namespace, such as the **[ExecutionProviderCatalog](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning.executionprovidercatalog)** class and its methods (which are Windows Runtime APIs).
* *ONNX Runtime APIs*. Windows ML implementations (in the *Microsoft.WindowsAppSDK.ML* NuGet package) of certain APIs from the ONNX Runtime (ORT). For documentation, see the [ONNX Runtime API docs](https://onnxruntime.ai/docs/api/). For example, the [OrtCompileApi struct](https://onnxruntime.ai/docs/api/c/struct_ort_compile_api.html). For code examples that use these APIs, and more links to documentation, see the [Use Windows ML to run the ResNet-50 model](./tutorial.md) tutorial.

## The Microsoft.WindowsAppSDK.ML NuGet package

The Microsoft Windows ML runtime provides APIs for machine learning and AI operations in Windows applications. The *Microsoft.WindowsAppSDK.ML* NuGet package provides the Windows ML runtime `.winmd` files for use in both C# and C++ projects.

## The pywinrt Python wheels

The Microsoft Windows ML runtime leverages the [pywinrt](https://github.com/pywinrt/pywinrt) project to provide Python access to the same Windows ML APIs. The package name is *winui3-Microsoft.Windows.AI.MachineLearning*. Additional packages are required to use Windows App SDK in python. For details, see the [Run ONNX models with Windows ML](./run-onnx-models.md) topic.

## Windows ML APIs

For API reference documentation, and code examples, see the **[Microsoft.Windows.AI.MachineLearning](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning)** namespace.

## Implementation notes

The Windows ML runtime is integrated with the Windows App SDK and relies on its deployment and bootstrapping mechanisms:

* Automatically discovers execution providers compatible with current hardware
* Manages package lifetime and updates
* Handles package registration and activation
* Supports different versions of execution providers

#### Framework-dependent deployment

Windows ML is delivered as a _framework-dependent_ component. This means your app must either:

* Reference the main Windows App SDK NuGet package by adding a reference to `Microsoft.WindowsAppSDK` (recommended)
* Or, reference both `Microsoft.WindowsAppSDK.ML` and `Microsoft.WindowsAppSDK.Runtime`

For more information on deploying Windows App SDK applications, see the [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview) documentation.

#### Using ONNX Runtime with Windows ML

For C++ applications, after registering execution providers, use the ONNX Runtime C API directly to create sessions and run inference.

For C# applications, use the ONNX Runtime directly for inference using the `Microsoft.ML.OnnxRuntime` namespace.

For Python applications, use the separate ONNX Runtime wheel (`onnxruntime`) for inference. For the experimental release, please use the `onnxruntime-winml==1.22.0.post2` package from index `https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/ORT-Nightly/pypi/simple`.

#### Python notes

**Initialize Windows App SDK**

All Windows ML calls should happen after the Windows App SDK is initialized. This can be done with the following code:

```python
from winui3.microsoft.windows.applicationmodel.dynamicdependency.bootstrap import (
    InitializeOptions,
    initialize
)
with initialize(options = InitializeOptions.ON_NO_MATCH_SHOW_UI):
    # Your Windows ML code here
```

**Registration happens out of Windows ML**

The ONNX runtime is designed in a way where the Python and native environments are separate. And native registration calls in the same process will not work for the Python environment. Thus, the registration of execution providers should be done with the Python API directly.

**Remove pywinrt's packed vcruntime**

The pywinrt project includes a msvcp140.dll in the winrt-runtime package. This may conflict with other packages. Please remove this dll to avoid this problem and install the missing vcruntime libraries with the [vc redistributable](/cpp/windows/latest-supported-vc-redist) 

## See also

* [Run ONNX models with Windows ML](./run-onnx-models.md)
* [Windows App SDK Documentation](/windows/apps/windows-app-sdk/)
* [Windows App SDK on GitHub](https://github.com/microsoft/WindowsAppSDK)
* [Windows ML Samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsML)
* [ONNX Runtime API Documentation](https://onnxruntime.ai/docs/api/)
* [Package and deploy Windows apps](/windows/apps/package-and-deploy/deploy-overview)
