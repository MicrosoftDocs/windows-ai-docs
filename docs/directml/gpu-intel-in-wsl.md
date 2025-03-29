---
title: Enable Intel Xe graphics on WSL 2
description: Enable the Intel Xe graphics on the Windows Subsystem for Linux
ms.topic: article
ms.date: 03/29/2025
---

# Enable Intel Xe graphics on WSL

Windows 11 and later updates of Windows 10 support running existing ML tools, libraries, and popular frameworks that use Intel Xe graphics for GPU hardware acceleration inside a Windows Subsystem for Linux (WSL) instance.

## Install Windows 11 or Windows 10, version 21H2

To use these features, you can download and install [Windows 11](https://microsoft.com/software-download/windows11) or [Windows 10, version 21H2](https://microsoft.com/software-download/windows10).

## Install the GPU driver 

Download and install the [Configure WSL 2 for GPU Workflows](https://www.intel.com/content/www/us/en/docs/oneapi/installation-guide-linux/2023-0/configure-wsl-2-for-gpu-workflows.html) to use with your existing workflows.

## Install WSL

Once you've installed the above driver, ensure you [enable WSL](/windows/wsl/install-win10) and [install a glibc-based distribution](/windows/wsl/install-win10#install-your-linux-distribution-of-choice), such as Ubuntu or Debian. Ensure you have the latest kernel by selecting **Check for updates** in the **Windows Update** section of the Settings app. 

> [!NOTE]
> Ensure you have **Receive updates for other Microsoft products** enabled. You can find it in **Advanced options** within the **Windows Update** section of the Settings app. 

For these features, you need a kernel version of 5.10.43.3 or higher. You can check the version number by running the following command in PowerShell. 

```powershell
wsl cat /proc/version
```
