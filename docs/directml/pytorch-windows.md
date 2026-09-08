---
title: Enable PyTorch with DirectML on Windows
description: Learn how to set up PyTorch with DirectML on Windows to accelerate machine learning training and inference on DirectX 12-capable GPUs.
ms.date: 09/08/2026
ms.topic: how-to
---

# Enable PyTorch with DirectML on Windows

[PyTorch](https://pytorch.org/) with DirectML enables training and inference on DirectX 12-capable GPUs. PyTorch with DirectML is in public preview and works on native Windows starting with Windows 10 version 1709.

## Check your version of Windows

To check your Windows version and build number, select **Windows logo key** + **R**, enter `winver`, and select **OK**. Update to Windows 10 version 1709 or later if your build is older.

## Check for GPU driver updates

Install the latest driver available for your GPU through Windows Update or your hardware manufacturer's website.

## Set up Python

Install a Python environment. If you use [Miniconda](https://docs.conda.io/projects/miniconda/en/latest/), download and run the Windows installer for your architecture.

Then create and activate an environment named `pytorch-directml`:

```console
conda create --name pytorch-directml python=3.10
conda activate pytorch-directml
```

[!INCLUDE [Install and verify PyTorch with DirectML](includes/pytorch-directml-install-verify.md)]
