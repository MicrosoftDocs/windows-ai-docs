---
author: alvinashcraft
title: Get started with Windows AI Studio
description: Install and use Windows AI Studio to fine tune and deploy AI models on Windows and WSL.
ms.author: aashcraft
ms.date: 03/05/2024
ms.topic: article
keywords: windows 11, windows ai, windows ai studio, vs code, windows machine learning
---

# Get started with Windows AI Studio

**Windows AI Studio** simplifies generative AI app development by bringing together cutting-edge AI development tools and models from the Azure AI Studio catalog and other catalogs like Hugging Face. You can browse the AI models catalog powered by Azure ML and Hugging Face, download them locally, fine-tune, test, and use them in your Windows application. All of the computation happens locally, so please check whether your device meets the hardware requirements.

In this article, you'll learn how to install and configure Windows AI Studio so you're ready to fine tune, test, and use AI models on Windows and Windows Subsystem for Linux (WSL).

## Prerequisites

- **Visual Studio Code** or **Visual Studio Code - Insiders** must be installed. For more information, see [Download Visual Studio Code](https://code.visualstudio.com/download) and [Getting started with Visual Studio Code](https://code.visualstudio.com/docs/introvideos/basics).
- Install and configure **Windows Subsystem for Linux (WSL)** in Windows 10 version 2004 and higher (Build 19041 and higher) or Windows 11. See [How to install Linux on Windows with WSL](/windows/wsl/install) to get WSL and a default Linux distribution installed.
- WSL Ubuntu distribution 18.04 or greater should be installed and set to be the default distribution prior to using Windows AI Studio. Learn how to [change the default distribution](/windows/wsl/install#change-the-default-linux-distribution-installed).
- Windows AI Studio will run only on NVIDIA GPUs during the preview. So, please make sure to check your device specifications prior to installing the extension. Also ensure you have the latest [NVIDIA drivers](https://www.nvidia.com/Download/index.aspx) installed on your machine.
