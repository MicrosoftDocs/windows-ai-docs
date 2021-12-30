---
title: ONNX versions and Windows builds
description: Check which versions of ONNX are supported by each Windows 10 build. See a table summarizing the current Windows versions and the ONNX versions supported.
ms.date: 5/13/2020
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning, onnx
---

# ONNX versions and Windows builds

Windows Machine Learning supports specific versions of the ONNX format in released Windows builds. In order for your model to work with Windows ML, you will need to make sure your ONNX model version is supported for the Windows release targeted by your application.

The below table summarizes all currently released versions of Windows ML and the corresponding ONNX versions supported.

| Windows release | ONNX versions supported | ONNX opsets supported |
|-----------------|-------------------------|-----------------------|
| Windows 11, version 2104 | 1.2 - 1.7 | 7 - 12 | 
| Windows 10, version 2004 (build 19041) | 1.2.2, 1.3, and 1.4 | 7, 8, and 9 |
| Windows 10, version 1909 | 1.2.2 and 1.3 | 7 and 8 |
| Windows 10, version 1903 (build 18362) | 1.2.2 and 1.3 | 7 and 8 |
| Windows 10, version 1809 (build 17763) | 1.2.2 | 7 |

ONNX opset 10 is supported in the [NuGet package](https://www.nuget.org/packages/Microsoft.AI.MachineLearning/).

If you are developing using Windows Insider Flights builds, please check our [release notes](release-notes.md) for the minimum and maximum supported ONNX versions in flights of the Windows 10 SDK.

## ONNX opset converter

The ONNX API provides a library for converting ONNX models between different opset versions. This allows developers and data scientists to either upgrade an existing ONNX model to a newer version, or downgrade the model to an older version of the ONNX spec.

[The version converter](https://github.com/onnx/onnx/blob/master/docs/VersionConverter.md) may be invoked either via C++ or Python APIs. There is also a [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/ExportModelFromPyTorchToDifferentONNXOpsetVersions.md) that provides several examples on how to upgrade and downgrade an ONNX model to a new target opset.

[!INCLUDE [help](../includes/get-help.md)]
