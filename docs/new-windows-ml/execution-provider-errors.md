---
title: Troubleshoot execution provider download issues with Windows ML
description: Learn how to troubleshoot common issues when downloading and registering AI execution providers using Windows Machine Learning (ML).
ms.date: 11/02/2026
ms.topic: concept-article
---

# Troubleshoot execution provider download issues with Windows ML

The mechanism that Windows ML uses to acquire execution providers changed in [version 2.5.96 and greater of Windows ML](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.5.96). We highly recommend developers [update to the latest version of Windows ML](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning), or at least use [version 2.5.96 or greater](https://www.nuget.org/packages/Microsoft.Windows.AI.MachineLearning/2.5.96) to ensure the most reliable EP acquisition experience.

## [Windows ML 2.5.96 or greater](#tab/winml-post-2-5)

With Windows ML 2.5.96 or greater, execution providers are acquired through the Microsoft Store.

There are currently no known issues acquiring execution providers in Windows ML 2.5.96 and greater. If you find an issue, please report it as described in the [How to file feedback](#how-to-file-feedback) section below.

## [Windows ML 2.4.89 or earlier](#tab/winml-pre-2-5)

In versions 2.4.89 and earlier of Windows ML, execution providers were acquired through Windows Update.

With these versions of Windows ML, there are some known cases where you may see an error downloading and registering execution providers to use with Windows ML. This page covers common issues and potential ways you can resolve the issue.

If you can't resolve the issue, then please see the [How to file feedback](#how-to-file-feedback) section below. A Feedback Hub will automatically capture the logs that our team needs in order to investigate your issue.

### Upstream updates

In some cases, upstream Windows Updates will require a device reboot to resume EP download. If you encounter download errors, check if your device has pending updates that require a restart.

**Solution:** Restart your device to complete any pending Windows Updates, then try downloading the execution providers again.

### Windows updates paused

Devices with Windows Updates paused will not be able to download execution providers. Execution provider downloads rely on the Windows Update infrastructure to deliver components.

**Solution:** To resume Windows Updates, navigate to **Settings > Windows Update > Resume Updates**.

### Managed devices

In some cases, enterprise policies may prevent execution providers from being downloaded on managed devices. IT administrators may have configured policies that restrict component downloads or limit access to Windows Update.

**Solution:** Contact your IT administrator to verify whether enterprise policies are blocking execution provider downloads. They may need to adjust policies to allow these components.

---

## How to file feedback

If your issue wasn't resolved with the above suggestions, then you can file a report through Feedback Hub.

1. Launch Feedback Hub.
   - Select Start, enter Feedback Hub in the search bar, and select the Feedback Hub app from the list.
2. Select "Report a problem".
3. Describe your issue.
4. Select **Developer Platform** > **Windows Machine Learning** for the category.
5. Follow the steps to reproduce your problem and submit your issue.

## See also

- [Install Windows ML EPs](initialize-execution-providers.md)
- [Get started with Windows ML](get-started.md)
