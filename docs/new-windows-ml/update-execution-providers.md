---
title: Update execution providers in Windows ML
description: Learn how execution providers are updated in Windows ML.
ms.date: 01/06/2026
ms.topic: how-to
---

# Update execution providers in Windows ML

After an execution provider (EP) is installed (as seen in [install execution providers](./initialize-execution-providers.md)), the execution provider is updated through [Windows Update's optional nonsecurity preview releases, a.k.a. "D week releases"](/windows/deployment/update/release-cycle#optional-nonsecurity-preview-release).

These compatible updates are automatically made available to your app, without your app manually updating. This allows apps to benefit from performance improvements and support for new operators without requiring changes to your app.

There is currently no way for your app to programmatically install an updated EP. The updated EP will automatically be installed through Windows Update.

To see the release history of EPs, see [supported execution providers](./supported-execution-providers.md) and expand the "Release history" section underneath each available EP.

To check what version of EP a device has, see [check execution provider versions](./versioning.md).

## See also

* [Supported execution providers and release history](./supported-execution-providers.md)
* [Install execution providers](./initialize-execution-providers.md)
* [Check execution provider versions](./versioning.md)
* [Common execution provider download issues](./execution-provider-errors.md)