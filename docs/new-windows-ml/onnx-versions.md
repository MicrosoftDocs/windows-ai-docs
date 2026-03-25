---
title: ONNX Runtime versions shipped in Windows ML
description: Understand which versions of the ONNX Runtime were shipped in which versions of Windows ML.
ms.date: 03/20/2026
ms.topic: concept-article
---

# ONNX Runtime versions shipped in Windows ML

Each Windows App SDK release includes Windows ML, which includes a copy of the ONNX Runtime, so that your app can depend on a shared system-wide copy of the ONNX Runtime rather than distributing your own copy (if you choose framework-dependent deployment). See [Deploy your app](./distributing-your-app.md) for more details.

## Versions of ONNX Runtime in Windows ML

The following table clarifies which ONNX Runtime commit was shipped with each Windows App SDK release (which contains Windows ML).

Windows App SDK version | Windows App SDK release date | ONNX Runtime commit hash | ONNX Runtime date
--|--|--|--
2.0.0-Experimental6 | 3/13/2026 | [`058787c`](https://github.com/microsoft/onnxruntime/commit/058787ceead760166e3c50a0a4cba8a833a6f53f) (1.24.2) | 2/18/2026
2.0.0-Preview1 | 2/13/2026 | [`d7dffa0`](https://github.com/microsoft/onnxruntime/commit/d7dffa0e3c02214865a1ee31842c0afb6c18643f) (~1.24.0 RC) | 1/23/2026
2.0.0-Experimental5 | 2/13/2026 | [`d5379f5`](https://github.com/microsoft/onnxruntime/commit/d5379f5c11dc6ceb65002cc5c963a2ededbe407a) (~1.24.0) | 12/4/2025
2.0.0-Experimental4 | 1/13/2026 | [`d5379f5`](https://github.com/microsoft/onnxruntime/commit/d5379f5c11dc6ceb65002cc5c963a2ededbe407a) (~1.24.0) | 12/4/2025
1.8.6 | 3/19/2026 | [`8db19e1`](https://github.com/microsoft/onnxruntime/commit/8db19e1f2c4a100c77e4c198f4770bf2f2bb3532) (1.23.4) | 2/18/2026
1.8.5 | 2/10/2026 | [`58b2075`](https://github.com/microsoft/onnxruntime/commit/58b2075a54578015d2c573c209b2ca462228672d) (~1.23.3) | 1/20/2026
1.8.4 | 1/13/2026 | [`a83fc4d`](https://github.com/microsoft/onnxruntime/commit/a83fc4d58cb48eb68890dd689f94f28288cf2278) (~1.23.2) | 10/21/2025
1.8.3 | 11/11/2025 | [`a83fc4d`](https://github.com/microsoft/onnxruntime/commit/a83fc4d58cb48eb68890dd689f94f28288cf2278) (~1.23.2) | 10/21/2025
1.8.2 | 10/14/2025 | [`d9b2048`](https://github.com/microsoft/onnxruntime/commit/d9b2048791efb5804fe3d53a04b4971256addebf) (~1.23.1) | 9/26/2025
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
