---
title: Troubleshoot execution provider download issues with Windows ML
description: Learn how to troubleshoot common issues when downloading and registering AI execution providers using Windows Machine Learning (ML).
ms.date: 10/07/2025
ms.topic: concept-article
---

# Troubleshoot execution provider download issues with Windows ML

There are some cases where you may see an error downloading and registering execution providers to use with Windows ML. This page covers common issues and their solutions.

## Upstream updates

In some cases, upstream Windows Updates will require a device reboot to resume EP download. If you encounter download errors, check if your device has pending updates that require a restart.

**Solution:** Restart your device to complete any pending Windows Updates, then try downloading the execution providers again.

## Windows updates paused

Devices with Windows Updates paused will not be able to download execution providers. Execution provider downloads rely on the Windows Update infrastructure to deliver components.

**Solution:** To resume Windows Updates, navigate to **Settings > Windows Update > Resume Updates**.

## Windows Insiders in the Canary Channel

Windows ML execution providers are not yet available for Windows Insiders in the External Canary ring. This is a known limitation for devices enrolled in early preview builds.

**Solution:** To check if this applies to you, navigate to **Settings > Windows Update > Windows Insider Program**. If you're in the Canary Channel and need execution providers, consider switching to a different Windows Insider channel or using a stable Windows build.

## Managed devices

In some cases, enterprise policies may prevent execution providers from being downloaded on managed devices. IT administrators may have configured policies that restrict component downloads or limit access to Windows Update.

**Solution:** Contact your IT administrator to verify whether enterprise policies are blocking execution provider downloads. They may need to adjust policies to allow these components.

## See also

- [Initialize execution providers with Windows ML](initialize-execution-providers.md)
- [Get started with Windows ML](get-started.md)