---
title: ONNX Runtime versions shipped in Windows ML
description: Understand which versions of the ONNX Runtime were shipped in which versions of Windows ML.
ms.date: 06/24/2026
ms.topic: concept-article
---

# ONNX Runtime versions shipped in Windows ML

Windows ML packages include a copy of the ONNX Runtime, so that your app can depend on an optional shared system-wide copy of the ONNX Runtime rather than distributing your own copy (if you choose framework-dependent deployment). See [Install and deploy Windows ML](./distributing-your-app.md) for more details.

## Versions of ONNX Runtime in Windows ML

The following tables clarify which ONNX Runtime (ORT) commit was shipped with each Windows ML package release.

> [!NOTE]
> Only the **current release** is officially supported. Past and preview/experimental versions are shown for release history only.

### [Windows ML 2.x](#tab/winml2)

The 2.x versions of Windows ML ship ORT version 1.24 or higher via the [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) NuGet package. If you're using [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML), check that package's dependency on Microsoft.Windows.AI.MachineLearning to determine which ORT version applies to your app.

#### Current release

| [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) version | Release date | ONNX Runtime commit hash | ONNX Runtime date |
|--|--|--|--|
| `2.1.71` | 6/10/2026 | [`800ac32`](https://github.com/microsoft/onnxruntime/commit/800ac32bc82d562c611d641b1112a8aa9f90c4f9) (1.24.6) | 4/28/2026 |

<details><summary><strong>Release history (including preview/experimental)</strong></summary>

| [Microsoft.Windows.AI.MachineLearning](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning) version | Release date | ONNX Runtime commit hash | ONNX Runtime date |
|--|--|--|--|
| `2.1.75-experimental` | 6/8/2026 | [`94bc0cd`](https://github.com/microsoft/onnxruntime/commit/94bc0cd2b1e4e3321f61137809cd0e58abc1d3c5) (1.25.2) | 5/10/2026 |
| `2.1.70` | 6/8/2026 | [`800ac32`](https://github.com/microsoft/onnxruntime/commit/800ac32bc82d562c611d641b1112a8aa9f90c4f9) (1.24.6) | 4/28/2026 |
| `2.1.6` | 5/28/2026 | [`800ac32`](https://github.com/microsoft/onnxruntime/commit/800ac32bc82d562c611d641b1112a8aa9f90c4f9) (1.24.6) | 4/28/2026 |
| `2.1.3-experimental` | 5/18/2026 | [`94bc0cd`](https://github.com/microsoft/onnxruntime/commit/94bc0cd2b1e4e3321f61137809cd0e58abc1d3c5) (1.25.2) | 5/10/2026 |
| `2.1.1` | 5/18/2026 | [`800ac32`](https://github.com/microsoft/onnxruntime/commit/800ac32bc82d562c611d641b1112a8aa9f90c4f9) (1.24.6) | 4/28/2026 |
| `2.0.325-experimental` | 4/21/2026 | [`ef605cd`](https://github.com/microsoft/onnxruntime/commit/ef605cd9fa4686eb0578b2ad3958d3cfd6add993) (1.24.5) | 4/16/2026 |
| `2.0.300` | 4/28/2026 | [`ef605cd`](https://github.com/microsoft/onnxruntime/commit/ef605cd9fa4686eb0578b2ad3958d3cfd6add993) (1.24.5) | 4/16/2026 |
| `2.0.297-preview` | 3/26/2026 | [`2d92497`](https://github.com/microsoft/onnxruntime/commit/2d924974ef147392ced8409d36bd6d2e7fcc8a74) (1.24.4) | 3/16/2026 |

</details>

### [Windows ML 1.8.x](#tab/winml1-8)

The 1.8.x versions of the [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) package ship with ONNX Runtime versions 1.23.x.

#### Current release

| [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) version | Release date | ONNX Runtime commit hash | ONNX Runtime date |
|--|--|--|--|
| `1.8.2197` | 5/12/2026 | [`840c8d7`](https://github.com/microsoft/onnxruntime/commit/840c8d74c10934f15438cbf32f975bb6e74c3725) (1.23.5) | 3/31/2026 |

<details><summary><strong>Release history (including preview/experimental)</strong></summary>

| [Microsoft.WindowsAppSDK.ML](https://www.nuget.org/packages/Microsoft.WindowsAppSDK.ML) version | Release date | ONNX Runtime commit hash | ONNX Runtime date |
|--|--|--|--|
| `1.8.2192` | 4/21/2026 | [`840c8d7`](https://github.com/microsoft/onnxruntime/commit/840c8d74c10934f15438cbf32f975bb6e74c3725) (1.23.5) | 3/31/2026 |
| `1.8.2141` | 3/18/2026 | [`8db19e1`](https://github.com/microsoft/onnxruntime/commit/8db19e1f2c4a100c77e4c198f4770bf2f2bb3532) (1.23.4) | 2/18/2026 |
| `1.8.2124` | 2/10/2026 | [`58b2075`](https://github.com/microsoft/onnxruntime/commit/58b2075a54578015d2c573c209b2ca462228672d) (~1.23.3) | 1/20/2026 |
| `1.8.2119` | 1/13/2026 | [`a83fc4d`](https://github.com/microsoft/onnxruntime/commit/a83fc4d58cb48eb68890dd689f94f28288cf2278) (~1.23.2) | 10/21/2025 |
| `1.8.2109` | 11/11/2025 | [`a83fc4d`](https://github.com/microsoft/onnxruntime/commit/a83fc4d58cb48eb68890dd689f94f28288cf2278) (~1.23.2) | 10/21/2025 |
| `1.8.2095` | 10/14/2025 | [`d9b2048`](https://github.com/microsoft/onnxruntime/commit/d9b2048791efb5804fe3d53a04b4971256addebf) (~1.23.1) | 9/26/2025 |
| `1.8.2091` | 9/23/2025 | [`a922003`](https://github.com/microsoft/onnxruntime/commit/a922003189b916d566154f1a156cbe0391381416) (~1.23.0) | 9/10/2025 |
| `1.8.126-experimental` | 7/8/2025 | 1.22.0 (with minor changes) | 5/9/2025 |

</details>

---

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
