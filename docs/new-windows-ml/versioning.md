---
title: Versioning of Execution Providers in Windows Machine Learning
description: Understand how Windows Machine Learning (ML) handles versioning of execution providers.
ms.date: 05/13/2025
ms.topic: concept-article
---

# Versioning of execution providers

The Windows ML runtime uses the latest compatible version of EPs matching the same major version (x.*.*). This allows apps to benefit from performance improvements and support for new operators without requiring changes to your app.

EP packages follow a semantic versioning (SemVer) approach:

* Major and minor version components are encoded into the *Package Name*.
* *Package Version* is used for patch versions.

That packaging approach enables flexible versioning while maintaining compatibility with Microsoft Store and MSIX deployment mechanisms.

## ABI stability

The primary interface between the Windows ML runtime and execution providers (EPs) is through the ONNX Runtime ABI. Any version of the Windows ML runtime carries a specific version of the ONNX Runtime implementing a particular ABI version. EP packages implementing that ABI version and later (within the same major version) will function properly.
