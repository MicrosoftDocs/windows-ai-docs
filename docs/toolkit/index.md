---
title: Overview for the AI Toolkit for Visual Studio Code
description: The AI Toolkit for Visual Studio Code provides tools and access to a model catalog to help jump-start local AI development and deployment.
ms.date: 08/05/2025
ms.topic: overview
no-loc: [VS Code, Visual Studio Code, REST, Windows AI Studio, Azure AI]
#customer intent: As a Windows developer, I want to learn about the AI Toolkit for Visual Studio Code so that I can use it to download and fine-tune AI models locally.
---

# What is the AI Toolkit for Visual Studio Code?

The AI Toolkit for Visual Studio Code (VS Code) is a VS Code extension that simplifies generative AI app development by bringing together cutting-edge AI development tools and models from the [Azure AI Foundry catalog](/azure/ai-studio/how-to/model-catalog-overview) and other catalogs like [Hugging Face](https://huggingface.co/models).

> [!NOTE]
> Additional documentation and tutorials for the AI Toolkit for VS Code are available in the VS Code documentation: [AI Toolkit for Visual Studio Code](https://code.visualstudio.com/docs/intelligentapps/overview). You'll find guidance on Playground, working with AI models, fine-tuning local and cloud-based models, and more.

## AI Toolkit features

The AI Toolkit for VS Code (AI Toolkit) enables you to:

- Download and run AI models locally. The AI Toolkit provides access to highly optimized models for the following platforms and hardware:
  - Windows 11 running with DirectML acceleration
  - Windows 11 running directly on the CPU
  - Windows 11 Copilot+ PCs running on the NPU
  - Linux with NVIDIA acceleration
  - Linux running directly on the CPU
  - *MacOS support is coming soon*
- Test models in an intuitive playground or in your application with a REST API.
- Fine-tune your AI model - locally or in the cloud (on a virtual machine) - to create new skills, improve reliability of responses, set the tone and format of the response. The AI Toolkit provides a guided walkthrough to fine-tune popular small-language models (SLMs) - like [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) and [Mistral](https://mistral.ai/).
- Deploy your AI feature either to the cloud or with an application that runs on a device.
- [Batch run prompts](https://code.visualstudio.com/docs/intelligentapps/bulkrun) for selected AI models in the Playground.

> [!NOTE]
> The *Deepseek R1 Distilled* model is optimized for the Neural Processing Unit (NPU) and available to download on Snapdragon powered Copilot+ PCs running Windows 11. For more information, see [Running Distilled DeepSeek R1 models locally on Copilot+ PCs, powered by Windows AI Foundry](https://blogs.windows.com/windowsdeveloper/2025/01/29/running-distilled-deepseek-r1-models-locally-on-copilot-pcs-powered-by-windows-copilot-runtime/).
>
> The AI Toolkit now supports a [Bring Your Own Model](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/bring-your-own-models-on-ai-toolkit---using-ollama-and-api-keys/4369411) approach. You can use the AI Toolkit to download your own models, such as [DeepSeek-R1](https://ollama.com/library/deepseek-r1), from catalogs like Ollama and run them locally.

Want to learn more about the AI Toolkit for VS Code? Dona Sarkar and John Lam provide an overview of the AI Toolkit in this video from Microsoft Build 2024:

> [!VIDEO https://learn-video.azurefd.net/vod/player?id=1ab78482-6422-4122-83bb-5acdab4548b4]

For the latest documentation and to download and use the AI Toolkit, please visit the [VS Code documentation](https://code.visualstudio.com/docs/intelligentapps/overview).

## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Model catalog and collections in Azure AI Foundry portal](/azure/ai-studio/how-to/model-catalog-overview)
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
