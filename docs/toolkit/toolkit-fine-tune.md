---
author: alvinashcraft
title: Fine-tune a model with the AI Toolkit for Visual Studio Code
description: Fine-tune AI models locally using the AI Toolkit for Visual Studio Code.
ms.author: aashcraft
ms.date: 05/13/2024
ms.topic: article
keywords: windows 11, windows ai, ai toolkit, vs code, windows machine learning
---

# Fine-tune a model with AI Toolkit for VS Code

The AI Toolkit for VS Code (AI Toolkit) is a VS Code extension that enables you to download, test, fine-tune, and deploy AI models with your apps or the cloud. For more information, see the [AI Toolkit overview](index.md).

In this article, you'll learn how to:

> [!div class="checklist"]
> - Set up a local environment to fine-tune.
> - Execute a fine-tuning job.

## Prerequisites

- Completed [Get started with AI Toolkit for Visual Studio Code](toolkit-getting-started.md).
- Install **Windows Subsystem for Linux (WSL)**. See [How to install Linux on Windows with WSL](/windows/wsl/install) to get WSL and a default Linux distribution installed. WSL Ubuntu distribution 18.04 or greater must be installed and set to be the default distribution prior to using AI Toolkit for VS Code. Learn how to [change the default distribution](/windows/wsl/install#change-the-default-linux-distribution-installed).
- AI Toolkit for VS Code will run only on NVIDIA GPUs during the preview. So, make sure to check your device specifications prior to installing the extension. Also ensure you have the latest [NVIDIA drivers](https://www.nvidia.com/Download/index.aspx) installed on your machine. If you are given a choice between **Game Ready Driver** or **Studio Driver**, download the **Studio Driver**.

> [!NOTE]
> You'll need to know the model of your GPU to download the correct drivers. To find out which GPU you have, see [How to check your GPU and why it matters](https://www.microsoft.com/windows/learning-center/how-to-check-gpu).


## Environment set up

To check whether you have all the necessary prerequisites to run fine-tuning jobs on your local device or cloud VM, open the command palette (Shift+Control+P) and search **AI Toolkit: Validate Environment prerequisites**.

If your local device or cloud VM pass the validation checks, the **Setup WSL Environment** button will be enabled for you to select. This will install all the dependencies required to run fine-tuning jobs.

## Fine-tune

To start a new fine-tuning session using QLoRA, click on the **Model Fine-tuning** item in AI Toolkit.

> [!TIP]
> QLoRA combines quantization and low-rank adaptation (LoRA) to fine-tune models with your own data. Learn more about QLoRA at [QLoRA: Efficient Finetuning of Quantized LLMs](https://github.com/artidoro/qlora).


Start by entering a unique **Project Name** and a **Project Location**. A new folder with the specified project name will be created in the location you selected to store the project files.

Next, select a model - for example, **Phi-3-mini-4k-instruct** - from the **Model Catalog** and then select **Configure Project**:

:::image type="content" source="../images/toolkit-fine-tune/fine-tune-project.png" alt-text="Fine-tuning project setup":::

You'll then be prompted to configure your fine-tuning project settings. Ensure the **Fine-tune locally** checkbox is ticked (in the future the VS Code extension will allow you to offload fine-tuning to the cloud). :

:::image type="content" source="../images/toolkit-fine-tune/fine-tune-settings.png" alt-text="Fine-tune settings":::

### Model inference settings

There are two settings available in the model inference section:

| Setting | Description |
|---------|-------------|
| **Conda environment name** | The name of the conda environment to be activated and used for the fine-tuning process in WSL. **This name must be unique in your WSL distribution.** |
| **Inference prompt template** | Prompt template to be used at inference time. Make sure this matches the fine-tuned version. |

### Data settings

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


### Fine-tune settings

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

After all the parameters are set, click **Generate Project**. This will perform the following actions:

- Initiate the model download.
- Install all prerequisites and dependencies.
- Create VS Code workspace in WSL.

When the model is downloaded and the environment is ready, you can launch the project from AI Toolkit by selecting **Relaunch Window in Workspace** on the **Step 3 - Generating project** page. This will launch a new instance of VS Code connected to your WSL Ubuntu distribution.

> [!NOTE]
> You may be prompted to install additional extensions such as [Prompt flow for VS Code](https://marketplace.visualstudio.com/items?itemName=prompt-flow.prompt-flow) in the WSL instance of VS Code. For an optimal fine-tuning experience, install them to proceed.

You can now fine-tune the model using:

```bash
# replace {conda-env-name} with the name of the environment you set
conda activate {conda-env-name}
python finetuning/invoke_olive.py 
```

Checkpoints and final model will be saved in `models` folder of your project.

Next run inferencing with the fine-tuned model through chats in a `console`, `web browser` or `prompt flow`.

```bash
cd inference

# Console interface.
python console_chat.py

# Web browser interface allows to adjust a few parameters like max new token length, temperature and so on.
# User has to manually open the link (e.g. http://127.0.0.1:7860) in a browser after gradio initiates the connections.
python gradio_chat.py
```

> [!TIP]
> Instructions are also available in the `README.md` page, which can be found in the project folder.

## See Also

- [AI Toolkit overview](index.md)
- [Get started with AI Toolkit for Visual Studio Code](toolkit-getting-started.md)
- [Model fine-tuning concepts](..\fine-tuning.md)
