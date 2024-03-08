---
author: alvinashcraft
title: Get started with Windows AI Studio
description: Install and use Windows AI Studio to fine tune and deploy AI models on Windows and WSL.
ms.author: aashcraft
ms.date: 03/08/2024
ms.topic: article
keywords: windows 11, windows ai, windows ai studio, vs code, windows machine learning
---

# Get started with Windows AI Studio

**Windows AI Studio** simplifies generative AI app development by bringing together cutting-edge AI development tools and models from the Azure AI Studio catalog and other catalogs like Hugging Face. You can browse the AI models catalog powered by Azure ML and Hugging Face, download them locally, fine-tune, test, and use them in your Windows application. All of the computation happens locally, so please check whether your device meets the hardware requirements.

In this article, you'll learn how to install and configure Windows AI Studio so you're ready to fine tune, test, and use AI models on Windows and Windows Subsystem for Linux (WSL).

## Prerequisites

- **Visual Studio Code (VS Code)** or **VS Code - Insiders** must be installed. For more information, see [Download VS Code](https://code.visualstudio.com/download) and [Getting started with VS Code](https://code.visualstudio.com/docs/introvideos/basics).
- Install and configure **Windows Subsystem for Linux (WSL)** in Windows 10 version 2004 and higher (Build 19041 and higher) or Windows 11. See [How to install Linux on Windows with WSL](/windows/wsl/install) to get WSL and a default Linux distribution installed.
- WSL Ubuntu distribution 18.04 or greater should be installed and set to be the default distribution prior to using Windows AI Studio. Learn how to [change the default distribution](/windows/wsl/install#change-the-default-linux-distribution-installed).
- Windows AI Studio will run only on NVIDIA GPUs during the preview. So, please make sure to check your device specifications prior to installing the extension. Also ensure you have the latest [NVIDIA drivers](https://www.nvidia.com/Download/index.aspx) installed on your machine.

## Install Windows AI Studio

Now that you have the prerequisites installed, you can install the Windows AI Studio extension in VS Code.

1. Open VS Code.
1. In the **Extensions** view, search for **Windows AI Studio**.
1. Select the **Windows AI Studio** extension from the list and click **Install**.
1. After the installation is complete, click **Reload** to activate the extension.

## Prerequisites check and environment set up

After the Windows AI Studio is installed, the extension will check your system to make sure you have the right resources to run it. After the prerequisites check has completed successfully, you can complete the environment set up by clicking the **Set Up WSL Environment** button.

Now you're ready to use the extension. You'll be prompted to sign in to GitHub, so click **Allow** when prompted. You'll then be redirected to GitHub sign-in page. Please sign in and follow the process steps. After successful completion, you will be redirected to VS Code.

You're ready to start using Windows AI Studio. Let's explore the available actions.

## Available actions

Launch the Windows AI Studio extension by clicking on the Windows AI Studio icon in the Activity Bar on the side of the window. The Windows AI Studio extension provides the following features:

- **Model Fine-tuning**: Fine-tune the AI models from the Azure AI Studio catalog and other catalogs like Hugging Face.
- **RAG Project**: *(coming soon)*
- **Phi-2 Model Playground**: *(coming soon)*
- **Windows Optimized Models**: A collection of AI models already optimized for Windows.

:::image type="content" source="../images/windows-ai-studio-get-started-actions.png" alt-text="Windows AI Studio - Available Actions pane":::

### Model Fine-tuning

To start a new fine-tuning session using QLoRA, click on the **Model Fine-tuning** item in the Windows AI Studio extension. You can then select a model from the Azure AI Studio catalog or other catalogs like Hugging Face, and fine-tune it with your own data, powered by AzureML.

> [!NOTE]
> QLoRA combines quantization and low-rank adaptation (LoRA) to fine-tune models with your own data. Learn more about QLoRA at [QLoRA: Efficient Finetuning of Quantized LLMs](https://github.com/artidoro/qlora).

> [!NOTE]
> You do not need an Azure account to download the models from the Azure AI Studio catalog, however some other catalogs may require an account.

Start by entering a unique **Project Name** and a **Project Location**. A new folder with the specified project name will be created in the location you selected to store the project files.

Next, select a model from the **Model Catalog**.

:::image type="content" source="../images/windows-ai-studio-get-started-models.png" alt-text="Available models in the Windows AI Studio model catalog":::

You will be prompted to download the project template. You can then click "Configure Project" to adjust various settings. Windows AI Studio uses [Olive](https://microsoft.github.io/Olive/overview/olive.html) to run QLoRA fine-tuning on a [PyTorch](https://pytorch.org/) model from its catalog. All the settings are preset with the default values to be optimized to run the fine-tuning process locally with an optimal use of memory, but the values can be adjusted for your own scenarios.

#### Model inference settings

There are two settings available in the model inference section:

| Setting | Description |
|---------|-------------|
| **Conda environment name** | The name of the conda environment to be activated and used for the fine-tuning process in WSL. **This name must be unique in your WSL distribution.** |
| **Inference prompt template** | Prompt template to be used at inference time. Make sure this matches the fine-tuned version. |

:::image type="content" source="../images/windows-ai-studio-get-started-inference-settings.png" alt-text="Model inference settings for fine-tuning in Windows AI Studio":::

#### Data settings

The following settings are available in the data section to configure the dataset information:

| Setting | Description |
|---------|-------------|
| **Dataset name** | The name of the dataset to be used for fine-tuning the model. |
| **Training split** | The training split name for your dataset. |
| **Dataset type** | The type of dataset to be used. |
| **Text columns** | The names of the columns in the dataset to populate the training prompt. |
| **Text template** | The prompt template to be used to fine tune the model. This uses replacement tokens from the **Text columns**. |
| **Corpus strategy** | Indicates if you want to **join** the samples or process them **line by line**. |
| **Source max length** | The maximum number of tokens per training sample. |
| **Pad to max length** | Add a PAD token to the training sample until the max number of tokens. |

:::image type="content" source="../images/windows-ai-studio-get-started-data-settings.png" alt-text="Data settings for fine-tuning in Windows AI Studio":::

#### Fine tune settings

The following settings are available in the Fine tune section to further configure the fine-tuning process:

| Settings | Data type | Default value | Description |
|--------|--------|--------|--------|
| Compute Dtype | Str | bfloat16 | The data type for model weights and adapter weights. For a 4bit quantized model, it's also the computation data type for the quantized modules. Valid values: bfloat16, float16, or float32. |
| Quant type | | nf4 | The quantization data type to use. Valid values: fp4 or nf4. |
| Double quant | Bool | yes | Whether to use nested quantization where the quantization constants from the first quantization are quantized again. |
| Lora r | Int | 64 | The Lora attention dimension. |
| Lora alpha | Float | 16 | The alpha parameter for Lora scaling. |
| Lora dropout | Float | 0.1 | The dropout probability for Lora layers. |
| Eval dataset size | Float | 1024 | The size of the validation dataset. |
| Seed | Int | 0 | A random seed for initialization. |
| Data Seed | Int | 42 | A random seed to be used with data samplers. |
| Per device train batch size | Int | 1 | The batch size per GPU for training. |
| Per device eval batch size | Int | 1 | The batch size per GPU for evaluation. |
| Gradient accumulation steps | Int | 4 | The number of updates steps to accumulate the gradients for, before performing a backward/update pass. |
| Enable Gradient checkpoint | Bool | yes | Use gradient checkpointing. The is recommended to save memory. |
| Learning rate | Float | 0.0002 | The initial learning rate for AdamW. |
| Max steps | Int | -1 | If set to a positive number, the total number of training steps to perform. This overrides num_train_epochs. In case of using a finite iterable dataset, the training may stop before reaching the set number of steps when all data is exhausted. |

:::image type="content" source="../images/windows-ai-studio-get-started-fine-tune-settings.png" alt-text="The fine tune settings for fine-tuning a model in Windows AI Studio":::

After all the parameters are set, click **Generate Project**. This will perform the following actions:

- Initiate the model download.
- Install all prerequisites and dependencies.
- Create VS Code workspace in WSL.

When the model is downloaded and the environment is ready, you can launch the project from Windows AI Studio by clicking **Relaunch Window in Workspace** on the **Step 3 - Generating project** page. This will launch a new instance of VS Code connected to your WSL Ubuntu distribution.

> [!NOTE]
> You may be prompted to install additional extensions such as [Prompt flow for VS Code](https://marketplace.visualstudio.com/items?itemName=prompt-flow.prompt-flow) in the WSL instance of VS Code. For an optimal fine-tuning experience, please install them to proceed.

You can now fine-tune and test the model by following the instructions in the `README.md` page, which can be found in the project folder.

:::image type="content" source="../images/windows-ai-studio-get-started-wsl-readme.png" alt-text="View the readme.md file in WSL in VS Code":::

You can explore the project structure in VS Code's **Explorer** view, including folders like `finetuning`, `dataset`, `inference`, and `setup`.

Learn more about fine-tuning models in Windows AI Studio:

- For a default dataset walkthrough see [Streamlined Model Fine-Tuning with Windows AI Studio in VS Code](https://github.com/microsoft/windows-ai-studio/blob/main/walkthrough-simple-dataset.md).
- For a Hugging Face dataset walkthrough see [Model Fine-Tuning using Hugging Face dataset](https://github.com/microsoft/windows-ai-studio/blob/main/walkthrough-hf-dataset.md).

### RAG Project

*Coming soon!*

### Phi-2 Model Playground

*Coming soon!*

### Windows Optimized Models

This is the collection of publicly available AI models already optimized for Windows. The models are stored in various locations including Hugging Face, GitHub and others. With Windows AI Studio, you can browse the models and find them all in one place ready to be downloaded and used in your Windows application.

:::image type="content" source="../images/windows-ai-studio-get-started-optimized-models.png" alt-text="Windows optimized models in Windows AI Studio":::

## See also

[Windows AI Studio](index.md)

[Windows AI Studio on GitHub](https://github.com/microsoft/windows-ai-studio/)

[Azure AI Studio](https://azure.microsoft.com/products/ai-studio/)

[Models - Hugging Face](https://huggingface.co/models)
