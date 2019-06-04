---
author: eliotcowley
title: ONNX versions and Windows builds
description: Check which versions of ONNX are supported by each Windows 10 build.
ms.author: elcowle
ms.date: 6/4/2019
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

[!INCLUDE [help](../includes/get-help.md)]
