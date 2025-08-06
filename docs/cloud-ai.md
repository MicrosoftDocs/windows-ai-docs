---
title: Choose between cloud-based and local AI models
description: Guidance to help developers that want help choosing between cloud-based and local AI models for their Windows applications.
ms.author: mattwoj
author: mattwojo
reviewer: mousma
ms.date: 08/05/2025
ms.topic: overview
no-loc: [Windows, Phi, Phi Silica, Azure, Copilot, Windows AI Foundry, Foundry Local, Azure, Azure AI Foundry]
---

# Choose between cloud-based and local AI models

For app developers seeking to integrate AI features, Microsoft Windows offers a comprehensive and flexible platform that supports both local, on-device processing and scalable, cloud-based solutions.

Choosing between cloud-based and local AI models depends on your specific needs and priorities. Factors to consider include:

- Data privacy, compliance, and security
- Resource availability
- Accessibility and collaboration
- Cost
- Maintenance and updates
- Performance & latency
- Scalability
- Connectivity requirements
- Model size and complexity
- Tooling and the associated ecosystem
- Customization and control

## Key decision factors for app developers

- **Data privacy, compliance, and security**
  
  - - **Local, on-premises:** Since data remains on the device, running a model locally can offer benefits regarding security and privacy, with the responsibility of data security resting on the user. The developer holds responsibility for managing updates, ensuring compatibility, and monitoring security vulnerabilities.
  
  - - **Cloud:** Cloud providers offer robust security measures, but data needs to be transferred to the cloud, which might raise data privacy concerns for the business or app service maintainer in some cases. Sending data to the cloud also must comply with data protection regulations, such as GDPR or HIPAA, depending on the nature of the data and the region in which the app operates. Cloud providers typically handle security updates and maintenance, but users must ensure that they are using secure APIs and following best practices for data handling.

- **Resource availability**

  - **Local, on-premises:** Running a model depends on the resources available on the device being used, including the CPU, GPU, NPU, memory, and storage capacity. This can be limiting if the device does not have high computational power or sufficient storage. Small Language Models (SLMs), like [Phi](./apis/phi-silica.md), are more ideal for use locally on a device. [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs) offer built-in models run by [Windows AI Foundry](./apis/index.md) with ready-to-use AI features.
  
  - - **Cloud:** Cloud platforms, such as [Azure AI Services](/azure/ai-services/), offer scalable resources. You can use as much computational power or storage as you need and only pay for what you use. Large Language Models (LLMs), like the [OpenAI language models](https://platform.openai.com/docs/models), require more resources, but are also more powerful.

- **Accessibility and collaboration**
  
  - - **Local, on-premises:** The model and data are accessible only on the device unless shared manually. This has the potential to make collaboration on model data more challenging.
  
  - - **Cloud:** The model and data can be accessed from anywhere with internet connectivity. This may be better for collaboration scenarios.

- **Cost**

  - **Local, on-premises:** There is no additional cost beyond the initial investment in the device hardware.

  - **Cloud:** While cloud platforms operate on a pay-as-you-go model, costs can accumulate based on the resources used and the duration of usage.

- **Maintenance and Updates**

  - **Local, on-premises:** The user is responsible for maintaining the system and installing updates.

  - **Cloud:** Maintenance, system updates, and new feature updates are handled by the cloud service provider, reducing maintenance overhead for the user.

- **Performance & Latency**

  - **Local, on-premises:** Running a model locally can reduce latency since data does not need to be sent over the network. However, performance is limited by the device's hardware capabilities.

  - **Cloud:** Cloud-based models can leverage powerful hardware, but they may introduce latency due to network communication. The performance can vary based on the user's internet connection and the cloud service's response time.

- **Scalability**

  - **Local, on-premises:** Scaling a model on a local device may require significant hardware upgrades or the addition of more devices, which can be costly and time-consuming.

  - **Cloud:** Cloud platforms offer easy scalability, allowing you to quickly adjust resources based on demand without the need for physical hardware changes.

- **Connectivity Requirements**

  - **Local, on-premises:** A local device does not require an internet connection to run a model, which can be beneficial in environments with limited connectivity.

  - **Cloud:** Cloud-based models require a stable internet connection for access and may be affected by network issues.

- **Model Size and Complexity**

  - **Local, on-premises:** Local devices may have limitations on the size and complexity of models that can be run due to hardware constraints. Smaller models, such as [Phi](./apis/phi-silica.md), are more suitable for local execution.

  - **Cloud:** Cloud platforms can handle larger and more complex models, such as those provided by OpenAI, due to their scalable infrastructure.

- **Tooling and the associated ecosystem**

  - **Local, on-premises:** Local AI solutions, such as Windows AI Foundry, Windows ML, and Foundry Local, integrate with Windows App SDK and ONNX Runtime, allowing developers to embed models directly into desktop or edge apps with minimal external dependencies.

  - **Cloud:** Cloud AI solutions, such as Azure AI Foundry, Azure AI Services, and Azure OpenAI Service, provide a comprehensive set of APIs and SDKs for building AI applications. These services are designed to integrate seamlessly with Azure DevOps, GitHub Copilot, Semantic Kernel, and other Azure services, enabling end-to-end orchestration, model deployment, and monitoring at scale.

- **Customization and Control**

  - **Local, on-premises:** Local models can be used out-of-the-box, without requiring a high level of expertise. Windows AI Foundry offers models like [Phi Silica](../docs/apis/phi-silica.md) that are ready to use. Alternatively, [Windows ML](./new-windows-ml/overview.md) allows developers to run custom models, such as those trained with ONNX Runtime, directly on Windows devices. This provides a high level of control over the model and its behavior, allowing for fine-tuning and optimization based on specific use cases. [Foundry Local](../docs/foundry-local/get-started.md) also allows developers to run models locally on Windows devices, providing a high level of control over the model and its behavior.

  - **Cloud:** Cloud-based models also offer both ready-to-use and customizable options, allowing developers to leverage pre-trained capabilities while still tailoring the model to their specific needs. [Azure AI Foundry](/azure/ai-foundry/what-is-azure-ai-foundry) is a unified Azure platform-as-a-service offering for enterprise AI operations, model builders, and application development. This foundation combines production-grade infrastructure with friendly interfaces, enabling developers to focus on building applications rather than managing infrastructure.

## Cloud AI Samples

If a cloud-based solution works better for your Windows app scenario, you may be interested in some of the tutorials below.

Many APIs are available for accessing cloud-based models to power AI features in your Windows app, whether those models are customized or ready-to-use. Using a cloud-based model can allow your app to remain streamlined by delegating resource-intensive tasks to the cloud. A few resources to help you add cloud-based AI-backed APIs offered by Microsoft or OpenAI include:

- [**Add OpenAI chat completions to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/chatgpt-openai-winui3): A tutorial on how to integrate the cloud-based OpenAI ChatGPT completion capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Add DALL-E to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/dall-e-winui3): A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Create a recommendation app with .NET MAUI and ChatGPT**](./samples/tutorial-maui-ai.md): A tutorial on how to create a sample Recommendation app that integrates the cloud-based OpenAI ChatGPT completion capabilities into a .NET MAUI app.

- [**Add DALL-E to your .NET MAUI Windows desktop app**](./samples/dall-e-maui-windows.md):  A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a .NET MAUI app.

- [**Azure OpenAI Service**](/azure/ai-services/openai/): If you want your Windows app to access OpenAI models, such as GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, DALLE-3 or the Embeddings model series, with the added security and enterprise capabilities of Azure, you can find guidance in this Azure OpenAI documentation.

- [**Azure AI Services**](/azure/ai-services/what-are-ai-services): Azure offers an entire suite of AI services available through REST APIs and client library SDKs in popular development languages. For more information, see each service's documentation. These cloud-based services help developers and organizations rapidly create intelligent, cutting-edge, market-ready, and responsible applications with out-of-the-box and prebuilt and customizable APIs and models. Example applications include natural language processing for conversations, search, monitoring, translation, speech, vision, and decision-making.