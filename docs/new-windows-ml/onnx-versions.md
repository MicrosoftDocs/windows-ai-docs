---
title: ONNX Runtime versions shipped in Windows ML
description: Understand which versions of the ONNX Runtime were shipped in which versions of Windows ML.
ms.date: 09/22/2025
ms.topic: concept-article
---

# ONNX Runtime versions shipped in Windows ML

Each Windows App SDK release includes Windows ML, which includes a copy of the ONNX Runtime, so that your app can depend on a shared system-wide copy of the ONNX Runtime rather than distributing your own copy.

## Versions of ONNX Runtime in Windows ML

The following table clarifies which ONNX Runtime commit was shipped with each Windows App SDK release (which contains Windows ML).

Windows App SDK version | Windows App SDK release date | ONNX Runtime commit hash | ONNX Runtime date
--|--|--|--
1.8.1 | 9/22/2025 | [`a922003`](https://github.com/microsoft/onnxruntime/commit/a922003189b916d566154f1a156cbe0391381416) (~1.23.0) | 9/10/2025
1.8.0-Experimental4 | 7/8/2025 | 1.22.0 (with minor changes) | 5/9/2025

## Automatic updates for framework-dependent apps

If your app uses the [framework-dependent version](/windows/apps/package-and-deploy/deploy-overview#more-info-about-framework-dependent-deployment) of Windows App SDK, your app will automatically receive updates across the revision version number without re-compiling and updating your app, but not across minor or major versions.

The following table shows how automatic updates work across different **Windows App SDK** version number changes:

| Version Component | Example Change | Automatic Update? | Description |
|--|--|--|--|
| **Revision** (x.y.**Z**) | 1.8.0 → 1.8.**1** | ✅ Yes | Bug fixes and patches - automatically applied |
| **Revision** (x.y.**Z**) | 1.8.1 → 1.8.**2** | ✅ Yes | Bug fixes and patches - automatically applied |
| **Minor** (x.**Y**.z) | 1.**8**.2 → 1.**9**.0 | ❌ No | Significant update - requires manual update |
| **Major** (**X**.y.z) | **1**.9.0 → **2**.0.0 | ❌ No | Breaking changes - requires manual update |

### Version breakdown examples

| App Targets | Latest Available | App Actually Uses | Update Type |
|--|--|--|--|
| 1.8.0 | 1.8.3 | 1.8.3 | ✅ Automatic (revision) |
| 1.8.0 | 1.9.0 | 1.8.3* | ❌ Manual required (minor) |
| 1.8.0 | 2.0.0 | 1.8.3* | ❌ Manual required (major) |

_*Latest revision within the same minor version_

This means that if you target 1.8.0 of Windows App SDK and 1.8.1 is released, your app will automatically use 1.8.1 (and the corresponding Windows ML ONNX Runtime version). However, when 1.9.0 is released, your app will continue using 1.8.1 until you manually update your app to target 1.9.0.