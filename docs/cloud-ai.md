---
title: Using Cloud AI Models instead of Windows Copilot Runtime
description: Guidance to help developers that want to use cloud models and AI features
ms.author: mattwoj
author: mousma
ms.date: 02/24/2025
ms.topic: overview
no-loc: [Windows Copilot Runtime, Azure, Azure AI Foundry]
---

# Using Cloud AI Models and AI Features

Integrating AI into your Windows application can be achieved through two primary methods: a local model via Windows Copilot Runtime or a cloud-based model. There are several aspects to consider when determining the right option for your needs.

- **Resource Availability**
  - **Local Device:** Running a model depends on the resources available on the device being used, including the CPU, GPU, NPU, memory, and storage capacity. This can be limiting if the device does not have high computational power or sufficient storage. Small Language Models (SLMs), like [Phi](./apis/phi-silica.md), are more ideal for use locally on a device. [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs) offer built-in models run by [Windows Copilot Runtime](./apis/index.md) with ready-to-use AI features.
  - **Cloud:** Cloud platforms, such as [Azure AI Services](/azure/ai-services/), offer scalable resources. You can use as much computational power or storage as you need and only pay for what you use. Large Language Models (LLMs), like the [OpenAI language models](https://platform.openai.com/docs/models), require more resources, but are also more powerful.

- **Data Privacy and Security**
  - **Local Device:** Since data remains on the device, running a model locally can offer benefits regarding security and privacy, with the responsibility of data security resting on the user.
  - **Cloud:** Cloud providers offer robust security measures, but data needs to be transferred to the cloud, which might raise data privacy concerns for the business or app service maintainer in some cases.

- **Accessibility and Collaboration**
  - **Local Device:** The model and data are accessible only on the device unless shared manually. This has the potential to make collaboration on model data more challenging.
  - **Cloud:** The model and data can be accessed from anywhere with internet connectivity. This may be better for collaboration scenarios.

- **Cost**
  - **Local Device:** There is no additional cost beyond the initial investment in the device hardware.
  - **Cloud:** While cloud platforms operate on a pay-as-you-go model, costs can accumulate based on the resources used and the duration of usage.

- **Maintenance and Updates**
  - **Local Device:** The user is responsible for maintaining the system and installing updates.
  - **Cloud:** Maintenance, system updates, and new feature updates are handled by the cloud service provider, reducing maintenance overhead for the user.

### Azure AI Foundry for Enterprises

[Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/what-is-azure-ai-foundry) provides a unified platform for enterprise AI operations, model builders, and application development. This foundation combines production-grade infrastructure with friendly interfaces, ensuring organizations can build and operate AI applications with confidence.

### Cloud AI Samples

If a cloud-based solution works better for your Windows app scenario, you may be interested in some of the tutorials below.

Many APIs are available for accessing cloud-based models to power AI features in your Windows app, whether those models are customized or ready-to-use. Using a cloud-based model can allow your app to remain streamlined by delegating resource-intensive tasks to the cloud. A few resources to help you add cloud-based AI-backed APIs offered by Microsoft or OpenAI include:

- [**Add OpenAI chat completions to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/chatgpt-openai-winui3): A tutorial on how to integrate the cloud-based OpenAI ChatGPT completion capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Add DALL-E to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/dall-e-winui3): A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Create a recommendation app with .NET MAUI and ChatGPT**](./samples/tutorial-maui-ai.md): A tutorial on how to create a sample Recommendation app that integrates the cloud-based OpenAI ChatGPT completion capabilities into a .NET MAUI app.

- [**Add DALL-E to your .NET MAUI Windows desktop app**](./samples/dall-e-maui-windows.md):  A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a .NET MAUI app.

- [**Azure OpenAI Service**](/azure/ai-services/openai/): If you want your Windows app to access OpenAI models, such as GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, DALLE-3 or the Embeddings model series, with the added security and enterprise capabilities of Azure, you can find guidance in this Azure OpenAI documentation.

- [**Azure AI Services**](/azure/ai-services/what-are-ai-services): Azure offers an entire suite of AI services available through REST APIs and client library SDKs in popular development languages. For more information, see each service's documentation. These cloud-based services help developers and organizations rapidly create intelligent, cutting-edge, market-ready, and responsible applications with out-of-the-box and prebuilt and customizable APIs and models. Example applications include natural language processing for conversations, search, monitoring, translation, speech, vision, and decision-making.