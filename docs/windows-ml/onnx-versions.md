---
author: eliotcowley
title: ONNX versions and Windows builds
description: Check which versions of ONNX are supported by each Windows 10 build.
ms.author: elcowle
ms.date: 5/23/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, onnx
ms.localizationpriority: medium
---

# ONNX versions and Windows builds

Windows Machine Learning supports specific versions of the ONNX format in released Windows builds. In order for your model to work with Windows ML, you will need to make sure your ONNX model version is supported for the Windows release targeted by your application.

The below table summarizes all currently released versions of Windows ML and the corresponding ONNX versions supported.

| Windows release | ONNX versions supported | ONNX opsets supported |
|-----------------|-------------------------|-----------------------|
| Windows 10, version 1903 (build 18362) | 1.2.2 and 1.3 | 7 and 8 |
| Windows 10, version 1809 (build 17763) | 1.2.2 | 7 |

If you are developing using Windows Insider Flights builds, please check our [release notes](release-notes.md) for the minimum and maximum supported ONNX versions in flights of the Windows 10 SDK.

------------------------------------------------------------------------------------------------------------------

[The following paragraph to be included once SVC team updates their documentation]

When considering exporting or converting a model to the ONNX format, please keep in mind that not all operators supported in the native model format are supported in given ONNX versions and opsets. For example, you might run into a situation where trying to convert your TensorFlow model to ONNX 1.2 generates errors due to unsupported operators present in the model. To increase your chances of success in generating a valid ONNX file in the target version for your application, we recommend checking the following resources which contain specific information on supported operators for each ONNX opset, allowing you or your data scientist team to avoid unsupported operators during model development:

- PyTorch ONNX native export documentation

- TensorFlow converter supported operators documentation
    > [!NOTE]
    > The TensorFlow converter is available to WinML developers via [WinMLTools](https://pypi.org/project/winmltools/).

- Keras converter supported operators documentation
