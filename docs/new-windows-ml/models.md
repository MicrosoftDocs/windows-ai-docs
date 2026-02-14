---
title: Find or train models for Windows ML
description: Learn how to find ONNX models, convert models from other frameworks, optimize for execution providers, and train custom models for Windows ML.
ms.topic: concept-article
ms.date: 12/24/2025
---

# Find or train models for Windows ML

Windows ML works with ONNX format models, since Windows ML is simply a distribution mechanism providing the ONNX Runtime and hardware-specific execution providers. This means you can use millions of existing pre-trained models from various sources, or train your own models. This guide covers where to find, convert, or train ONNX models.

| Options | Details |
|-------|---------|
| **[1. Use models from AI Toolkit](#option-1-use-models-from-ai-toolkit)** | Choose from [over 20+ OSS models](https://github.com/microsoft/olive-recipes/blob/main/.aitk/docs/guide/ModelList.md) (including LLMs and other types of models) that are ready-to-optimize for use with Windows ML using [AI Toolkit's Conversion tool](https://code.visualstudio.com/docs/intelligentapps/modelconversion) |
| **[2. Use other existing ONNX models](#option-2-use-other-existing-onnx-models)** | Browse over 30,000+ [pre-trained ONNX models from Hugging Face](https://huggingface.co/models?library=onnx) or other sources |
| **[3. Convert existing models to ONNX format](#option-3-convert-existing-models-to-onnx-format)** | Browse over 2,400,000+ [pre-trained PyTorch / TensorFlow / etc models from Hugging Face](https://huggingface.co/models) or other sources and convert them to ONNX |
| **[4. Fine-tune existing models](#option-4-fine-tune-existing-models)** | Fine-tune over 2,400,000+ [pre-trained PyTorch / TensorFlow / etc models from Hugging Face](https://huggingface.co/models) or other sources to work better for your scenario (and convert them to ONNX format)
| **[5. Train models](#option-5-train-models)** | Train your own models in PyTorch, TensorFlow, or other frameworks, and convert them to ONNX |

You can also choose from dozens of ready-to-use AI models and APIs in Microsoft Foundry on Windows, which run via Windows ML. See [Use local AI with Microsoft Foundry on Windows](../overview.md) to learn more.

## Option 1: Use models from AI Toolkit

With [AI Toolkit's Conversion tool](https://code.visualstudio.com/docs/intelligentapps/modelconversion), there are dozens of LLMs and other types of models that are ready-to-optimize for use with Windows ML. By obtaining a model through AI Toolkit, you'll get a converted ONNX model that is optimized for the variety of hardware that Windows ML runs on.

To browse the available models, see [AI Toolkit's Model List](https://github.com/microsoft/olive-recipes/blob/main/.aitk/docs/guide/ModelList.md).

## Option 2: Use other existing ONNX models

[Hugging Face](https://huggingface.co/) hosts thousands of ONNX models that you can use with Windows ML. You can find ONNX models by:

1. Browsing the [Hugging Face Model Hub](https://huggingface.co/models)
2. Filtering by ["ONNX" in the library filter](https://huggingface.co/models?library=onnx)

You will need to find a model that is compatible with the ONNX Runtime version included in the version of Windows ML you are using. See [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md) to find out what version of ONNX Runtime you are using with Windows ML.

## Option 3: Convert existing models to ONNX format

Models from PyTorch, TensorFlow, or other frameworks can be converted to ONNX format and used with Windows ML.

[Hugging Face](https://huggingface.co/) hosts millions of models that you can convert and use with Windows ML.

You will need to convert the model to run with the ONNX Runtime version included in the version of Windows ML you are using. See [ONNX Runtime versions shipped in Windows ML](./onnx-versions.md) to find out what version of ONNX Runtime you are using with Windows ML.

To convert a model to ONNX format, see framework-specific documentation, for example:

* [Convert PyTorch models tutorial](https://docs.pytorch.org/tutorials/beginner/onnx/export_simple_model_to_onnx_tutorial.html)
* [Convert TensorFlow models tutorial](https://onnxruntime.ai/docs/tutorials/tf-get-started.html)

## Option 4: Fine-tune existing models

Many models on [Hugging Face](https://huggingface.co/) or other sources can be fine-tuned (following instructions on the model cards on Hugging Face). You can then subsequently convert the fine-tuned model to ONNX following the instructions in Option 3 above.

A popular way to fine-tune models is to use the [olive finetune command](https://microsoft.github.io/Olive/how-to/cli/cli-finetune.html). See the [Olive documentation](https://microsoft.github.io/Olive/index.html) to learn more about using Olive.

## Option 5: Train models

If you need a model for a specific task and can't find an existing model, you can train your own in PyTorch, TensorFlow, or other frameworks.

Once you've trained your model, follow the instructions in Option 3 above to convert your model to ONNX format.

## Next steps

Once you have an ONNX model, you can run it with Windows ML on your target devices.

- **[Install execution providers](./initialize-execution-providers.md)** - Download and install execution providers in Windows ML
- **[Run ONNX models](./run-onnx-models.md)** - Learn how to run inference with Windows ML

## Other solutions

As part of Microsoft Foundry on Windows, you can also choose from dozens of ready-to-use AI models and APIs, which run via Windows ML. See [Use local AI with Microsoft Foundry on Windows](../overview.md) to learn more.
