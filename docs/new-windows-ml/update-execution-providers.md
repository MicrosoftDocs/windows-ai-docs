---
title: Update execution providers in Windows ML
description: Learn how execution providers are updated in Windows ML.
ms.date: 11/02/2026
ms.topic: how-to
---

# Update execution providers in Windows ML

After an execution provider (EP) is installed (as seen in [Install Windows ML EPs](./initialize-execution-providers.md)), the execution provider is automatically updated by Windows ML.

These compatible updates are automatically made available to your app, without your app manually updating. This allows apps to benefit from performance improvements and support for new operators without requiring changes to your app.

There is currently no way for your app to programmatically install an updated EP. The updated EP will automatically be installed by Windows ML.

To see the release history of EPs, see [Windows ML execution providers releases](https://github.com/microsoft/WindowsML/wiki/Windows-ML-Execution-Provider-Releases).

To check what version of EP a device has, see [Check Windows ML EP versions](./versioning.md).

## See also

* [Windows ML EPs and release history](./supported-execution-providers.md)
* [Install Windows ML EPs](./initialize-execution-providers.md)
* [Check Windows ML EP versions](./versioning.md)
* [Common execution provider download issues](./execution-provider-errors.md)