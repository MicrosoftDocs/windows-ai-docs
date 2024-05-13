---
author: alvinashcraft
title: Get started with AI Toolkit for Visual Studio Code
description: Install and use AI Toolkit for Visual Studio Code to fine tune and deploy AI models on Windows and WSL.
ms.author: aashcraft
ms.date: 05/13/2024
ms.topic: article
keywords: windows 11, windows ai, ai toolkit, vs code, windows machine learning
---

# Get started with AI Toolkit for Visual Studio Code

The AI Toolkit for VS Code (AI Toolkit) is a VS Code extension that enables you to download, test, fine-tune, and deploy AI models with your apps or the cloud. For more information, see the [AI Toolkit overview](index.md).

In this article, you'll learn how to:

> [!div class="checklist"]
> - Install the AI Toolkit for VS Code
> - Download a model from the catalog
> - Run the model locally using the playground
> - Run the model locally using REST

## Prerequisites

- **VS Code** must be installed. For more information, see [Download VS Code](https://code.visualstudio.com/download) and [Getting started with VS Code](https://code.visualstudio.com/docs/introvideos/basics).

## Install

The [AI Toolkit is available in the Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-windows-ai-studio.windows-ai-studio) and can be installed like any other VS Code extension. If you're unfamiliar with installing VS Code extensions, follow these steps:

1. In the Activity Bar in VS Code select **Extensions**
1. In the Extensions Search bar type "AI Toolkit"
1. Select the "AI Toolkit for Visual Studio code"
1. Select **Install**

Once the extension has been installed you'll see the icon in your Activity Bar.

## Download a model from the catalog

The primary sidebar of the AI Toolkit is organized into **Models** and **Resources**. The **Playground** and **Fine-tuning** features are available in the Resources section. To get started select **Model Catalog**:

:::image type="content" source="../images/toolkit-getting-started/model_catalog.png" alt-text="AI Toolkit model catalog":::

> [!TIP]
> You'll notice that the model cards show the model size (with parameters and disk space), as well as the platform and hardware requirements. For optimized performance on Windows devices that have an *integrated GPU*, select model versions that only target Windows. This ensures you have a model optimized for DirectML. To check whether you have a GPU on your device, open **Task Manager** and select **Performance**. If there is no GPU resource you'll need to select models for **CPU-only**.

Next, download the following model depending the availability of a GPU on your device.

| GPU available | Model name | Size (GB) | Platform |
|---------|---------|--------|--------|
| No    |    Phi-3-mini-4k-cpu-int4-rtn-block-32-acc-level-4-onnx     | 2.72GB | Windows/Mac/Linux |
| Yes   |    Phi-3-mini-4k-directml-int4-awq-block-128-onnx     | 2.13GB | Windows |

> [!NOTE]
> The Phi3-mini model is approximately 2GB in size. It could take a few minutes to download, depending on your network speed.

## Run the model in the playground

In **Resources**, select **Model Playground**. This will open the Playground window in the editor. Next, select your downloaded model from the model picker and type the message *What is the golden ratio?* followed by **Enter** in the chat interface. You should see the model response streaming back to you:

:::image type="content" source="../images/toolkit-getting-started/playground2.png" alt-text="Playground selection":::

> [!WARNING]
> If you only have a **CPU** available but you selected the Phi-3-mini-4k-**directml**-int4-awq-**block-128**-onnx model, the model response will be *very slow*. You should instead download the CPU optimized version: Phi-3-mini-4k-**cpu**-int4-**rtn-block-32-acc-level-4**-onnx.

It is also possible to change:

- **Context Instructions:** Help the model understand the bigger picture of your request. This could be background information, examples/demonstrations of what you want or explaining the purpose of your task.
- **Inference parameters:**
  - *Maximum response length*: The maximum number of tokens the model will return.
  - *Temperature*: Model temperature is a parameter that controls how random a language model's output is. A higher temperature means the model takes more risks, giving you a diverse mix of words. On the other hand, a lower temperature makes the model play it safe, sticking to more focused and predictable responses.
  - *Top P*: Also known as nucleus sampling, is a setting that controls how many possible words or phrases the language model considers when predicting the next word
  - *Frequency penalty*: This parameter influences how often the model repeats words or phrases in its output. The higher the value (closer to 1.0) encourages the model to *avoid* repeating words or phrases.
  - *Presence penalty*: This parameter is used in generative AI models to encourage diversity and specificity in the generated text. A higher value (closer to 1.0) encourages the model to include more novel and diverse tokens. A lower value is more likely for the model to generate common or cliche phrases.

## Use the REST API in your application

The AI Toolkit comes with a local REST API web server (on port 5272) that uses the [OpenAI chat completions format](https://platform.openai.com/docs/api-reference/chat/create). This enables you to test your application locally without having to rely on a cloud AI model service. The following `curl` command shows how a model can be consumed over REST:

```bash
curl http://127.0.0.1:5272/v1/chat/completions -d '{
    "model": "Phi-3-mini-4k-directml-int4-awq-block-128-onnx",
    "messages": [
        {
            "role": "user",
            "content": "what is the golden ratio?"
        }
    ],
    "temperature": 0.7,
    "top_p": 1,
    "top_k": 10,
    "max_tokens": 100,
    "stream": true
}'
```

> [!NOTE]
> You will need to update the model field to Phi-3-mini-4k-cpu-int4-rtn-block-32-acc-level-4-onnx, if you downloaded the CPU version of the Phi3 model.
