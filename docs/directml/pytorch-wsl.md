---
title: Enable PyTorch with DirectML on WSL
description: Learn how to set up PyTorch with DirectML in WSL 2 to accelerate machine learning training and inference on DirectX 12-capable GPUs.
author: GrantMeStrength
ms.author: jken
ms.date: 09/08/2026
ms.topic: how-to
---

# Enable PyTorch with DirectML on WSL

[PyTorch](https://pytorch.org/) with DirectML enables training and inference on DirectX 12-capable GPUs in Windows Subsystem for Linux (WSL). PyTorch with DirectML is in public preview and works in WSL 2.

## Check your version of Windows

The `torch-directml` package in WSL 2 requires Windows 11, build 22000 or later. To check your Windows version and build number, select **Windows logo key** + **R**, enter `winver`, and select **OK**.

## Install WSL 2

To install the default Linux distribution with WSL 2, open PowerShell or Windows Command Prompt in administrator mode and run:

```powershell
wsl --install
```

Restart your machine when prompted. For distribution selection and other installation options, see [Install Linux on Windows with WSL](/windows/wsl/install).

## Check for GPU driver updates

Install the latest Windows driver available for your GPU through Windows Update or your hardware manufacturer's website. The Windows driver enables GPU acceleration in WSL; you don't need to install a separate Linux display driver.

## Set up Python

Install a Python environment in your WSL distribution. For example, run the following commands to install [Miniconda](https://docs.conda.io/projects/miniconda/en/latest/):

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

Then create and activate an environment named `pytorch-directml`:

```bash
conda create --name pytorch-directml python=3.10
conda activate pytorch-directml
```

[!INCLUDE [Install and verify PyTorch with DirectML](includes/pytorch-directml-install-verify.md)]
