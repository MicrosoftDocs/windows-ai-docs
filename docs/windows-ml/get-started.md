---
title: Get Started with Windows Machine Learning
description: Get started with Windows Machine Learning, and learn about the different available solutions.
ms.date: 2/12/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials
ms.custom: RS5
---

# Get Started with Windows Machine Learning

There's several ways to use Windows Machine Learning in your app. At the core, you just need a couple straightforward steps.

1. Get a trained Open Neural Network Exchange (ONNX) model, or convert models trained in other ML frameworks into ONNX with [ONNXMLTools](onnxmltools.md).

2. Add the ONNX model file to your application, or make it available in some other way on the target device.

3. Integrate the model into your application code, then build and deploy the application.

![Training environment, add model reference, application, Windows ML](../images/winml-flow.png)

## In-box vs NuGet WinML solutions

The table below highlights the availability, distribution, language support, servicing, and forward compatibility aspects of the In-Box and NuGet package for Windows ML. 

|Properties | In-Box | NuGet |
| --- | --- | --- |
| Availability | Windows 10 version 1809 or higher | Windows 8.1 or higher |
| Distribution | Built into the Windows SDK | Package and distribute as part of your application |
| Servicing | Microsoft-driven (customers benefit automatically) | Developer-driven |
| Forward compatibility | Automatically rolls forward with new features | Developer needs to update package manually |


:::row:::
    :::column:::
   When your application runs with the in-box solution, the Windows ML runtime (which contains the ONNX Model Inference Engine) evaluates the trained model on the Windows 10 device (or Windows Server 2019 if targeting a server deployment). Windows ML handles the hardware abstraction, allowing developers to target a broad range of silicon—including CPUs, GPUs, and, in the future, AI accelerators. Windows ML hardware acceleration is built on top of [DirectML](/windows/desktop/direct3d12/dml), a high-performance, low-level API for running ML inferences that is part of the DirectX family. 
    :::column-end:::
    :::column:::
        ![windows ml layers](../images/overview-diagram.svg)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
   ![windows ml nuget package](../images/winml-nuget.svg)
    :::column-end:::
    :::column:::
    For the NuGet package, these layers appear as binaries shown in the diagram below. Windows ML is built into the Microsoft.ai.machinelearning.dll. It does not contain an embedded ONNX runtime, instead the ONNX runtime is built into the file: onnxruntime.dll. The version included in the WindowsAI NuGet packages contains a DirectML EP embedded inside of it. The final binary, DirectML.dll, is the actual platform code as DirectML and is built on top of the Direct 3D and compute drivers that are built into Windows. All three of these binaries are included in the NuGet releases for you to distribute along with your applications. 

   Direct access to the onnxruntime.dll also allows you to target cross-platform scenarios while getting the same hardware agnostic acceleration that scales across all Windows devices. 
    :::column-end:::
:::row-end:::

## Other machine learning solutions from Microsoft

Microsoft offers a variety of machine learning solutions to suit your needs. These solutions run in the cloud, on-premises, and locally on the device. See [What are the machine learning product options from Microsoft?](/azure/machine-learning/service/overview-more-machine-learning) for more information.

## Learn more

If you want to use the Windows ML NuGet package, please see [Tutorial: Port an Existing WinML App to NuGet Package](port-app-to-nuget.md).

For the latest Windows ML features and fixes, see our [release notes](release-notes.md).

[!INCLUDE [help](../includes/get-help.md)]
