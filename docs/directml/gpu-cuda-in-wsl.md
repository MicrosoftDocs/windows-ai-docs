---
title: Enable NVIDIA CUDA on WSL 2
description: Enable the NVIDIA CUDA preview on the Windows Subsystem for Linux
ms.topic: article
ms.date: 06/28/2024
---

# Enable NVIDIA CUDA on WSL

Windows 11 and later updates of Windows 10 support running existing ML tools, libraries, and popular frameworks that use NVIDIA CUDA for GPU hardware acceleration inside a Windows Subsystem for Linux (WSL) instance. This includes PyTorch and TensorFlow as well as all the Docker and NVIDIA Container Toolkit support available in a native Linux environment.

## Install Windows 11 or Windows 10, version 21H2

To use these features, you can download and install [Windows 11](https://microsoft.com/software-download/windows11) or [Windows 10, version 21H2](https://microsoft.com/software-download/windows10).

## Install the GPU driver 

Download and install the [NVIDIA CUDA enabled driver for WSL](https://www.nvidia.com/download/index.aspx) to use with your existing CUDA ML workflows. For more info about which driver to install, see:

* [Getting Started with CUDA on WSL 2](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#getting-started-with-cuda-on-wsl)
* [CUDA on Windows Subsystem for Linux (WSL)](https://developer.nvidia.com/cuda/wsl)

## Install WSL

Once you've installed the above driver, ensure you [enable WSL](/windows/wsl/install-win10) and [install a glibc-based distribution](/windows/wsl/install-win10#install-your-linux-distribution-of-choice), such as Ubuntu or Debian. Ensure you have the latest kernel by selecting **Check for updates** in the **Windows Update** section of the Settings app. 

> [!NOTE]
> Ensure you have **Receive updates for other Microsoft products** enabled. You can find it in **Advanced options** within the **Windows Update** section of the Settings app. 

For these features, you need a kernel version of 5.10.43.3 or higher. You can check the version number by running the following command in PowerShell. 

```powershell
wsl cat /proc/version
```

## Get started with NVIDIA CUDA

Now follow the instructions in the [NVIDIA CUDA on WSL User Guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#getting-started-with-cuda-on-wsl-2) and you can start using your exisiting Linux workflows through [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker), or by installing [PyTorch](https://pytorch.org/get-started/locally/) or [TensorFlow](https://www.tensorflow.org/install/gpu) inside WSL. 

Share feedback on NVIDIA's support via their [Community forum for CUDA on WSL](https://forums.developer.nvidia.com/c/accelerated-computing/cuda/cuda-on-windows-subsystem-for-linux-wsl-2/303).
