---
title: Ready-to-use local LLMs in Microsoft Foundry on Windows
description: Learn how your Windows app can leverage ready-to-use local Large Language Models (LLMs) via Windows AI APIs and Foundry Local.
ms.topic: overview
ms.date: 01/13/2026
---

# Ready-to-use local LLMs in Microsoft Foundry on Windows

Microsoft Foundry on Windows provides multiple ready-to-use local Large Language Models (LLMs) that you can integrate into your Windows applications.

## Ready-to-use LLMs

Your app can effortlessly use the following local LLMs in less than an hour. Distribution of the LLM is handled by Microsoft, and the models are shared across apps. Using these LLMs only takes a handful of lines of code, zero ML-expertise needed.

| &nbsp; | What is it | Supported devices | Docs |
|--|--|--|--|
| **Phi Silica** | The same on-device LLM that inbox Windows experiences use | Copilot+ PCs (NPU) | [Learn more](./phi-silica.md) |
| **20+ open-source LLMs** | Choose from [over 20+ available OSS LLM models](https://www.foundrylocal.ai/models) | Windows 10+<br/><br/>*(Performance varies, not all models available on all devices)* | [Learn more](../foundry-local/get-started.md)

## Fine-tune local LLMs

If the above ready-to-use LLMs don't work for your scenario, you can fine-tune LLMs for your scenario. This option requires some work to build a fine-tuning training dataset, but is less work than training your own model.

* **Phi Silica**: See [LoRA Fine-Tuning for Phi Silica](./phi-silica-lora.md) to get started.

## Use LLMs from Hugging Face or other sources

You can use a wide variety of LLMs from Hugging Face or other sources, and run those locally on Windows 10+ PCs using [Windows ML](../new-windows-ml/overview.md) *(model compatibility and performance will vary based on device hardware)*. This option can be more complex and may take more time compared to the ready-to-use local LLMs.

See [find or train models for use with Windows ML](../new-windows-ml/models.md) to learn more.