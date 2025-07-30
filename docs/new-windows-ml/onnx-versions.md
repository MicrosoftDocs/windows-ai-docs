---
title: ONNX Runtime versions shipped in Windows ML
description: Understand which versions of the ONNX Runtime were shipped in which versions of Windows ML.
ms.date: 07/30/2025
ms.topic: concept-article
---

# ONNX Runtime versions shipped in Windows ML

Each Windows ML release includes a copy of the ONNX Runtime, so that your app can depend on a shared system-wide copy of the ONNX Runtime rather than distributing your own copy.

The following table clarifies what ONNX Runtime version was shipped with each WinML release.

WinML version | WinML release date | ONNX Runtime version | ONNX Runtime release date
--|--|--|--
1.8.126-experimental | 7/8/2025 | 1.22.0 (with minor changes) | 5/9/2025

Note that if your app uses the [framework-dependent version](/windows/apps/package-and-deploy/deploy-overview#more-info-about-framework-dependent-deployment) of Windows App SDK, your app will automatically receive updates across the revision version number. For example, if you target 1.8.0 of Windows ML, and 1.8.1 is released, your app will automatically be updated to use 1.8.1 (and whatever corresponding ONNX Runtime version that shipped with 1.8.1). However, your app will NOT automatically be updated across minor or major versions, so when 1.9.0 is released, your app would stay on 1.8.1 until you manually update your app to 1.9.0.