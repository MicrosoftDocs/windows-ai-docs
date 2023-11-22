---
title: Windows AI Studio Overview
description: Windows AI Studio simplifies generative AI app development brining together cutting-edge AI development tools and a model catalog, enabling developers to finetune, customize, and deploy SLMs for local use in their Windows apps. Currently available via preview as a VS Code extension.
author: matchamatch 
ms.author: mikben 
manager: jken
ms.topic: article
ms.date: 11/09/2023
---

# Windows AI Studio Overview

This is all just draft copy text for placement... actual documentation coming soon.

**Windows AI Studio** simplifies generative AI app development brining together cutting-edge AI development tools and a model catalog, enabling developers to finetune, customize, and deploy SLMs for local use in their Windows apps. Currently available via preview as a VS Code extension.

Note about being in Preview.

## What can I do with Windows AI Studio?

Download AI models to your local machine in order to fine-tune and optimize the model for integration into your Windows application.

## What AI models are available?

Currently a curated list of models from HuggingFace and Azure are available to try with the preview of Windows AI Studio, with more models to come as the product continues to develop.

## What is the Windows AI Studio process flow?

Select a model from the Windows AI Studio gallery > Fine-tune that model utlizing QLoRA through Olive to generate a quantized model with low-rank adapters > Model Evaluaion and validation for the fine-tuned task > Model Optimization using Onnx conversion and post-finetuneing optimizing using Olive > Model Integration deploying into your Windows application for inference using ORT.
