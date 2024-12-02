---
title: Get started using AI-backed APIs in your Windows app
description: Learn how to add the AI-backed Windows Copilot Runtime APIs to your Windows app.
ms.author: mattwoj
author: mattwojo
ms.date: 12/02/2024
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
---

# Get started using AI-backed APIs in your Windows app

**Windows Copilot Runtime** offers a variety of **AI-backed APIs** that enable you to tap into AI features without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows Copilot Runtime APIs are ready-to-use and passively running all the time on the device to enable AI features on Copilot+ PCs.

## Use Windows Copilot Runtime APIs

Windows Copilot Runtime APIs include AI-backed APIs powered by models running locally, directly on the Windows device. Learn more about the [Windows App SDK](/windows/apps/windows-app-sdk/).

- [**Phi Silica**](../apis/phi-silica.md): **Not yet available.** The Phi Silica API is available as a part of the [Windows App SDK](/windows/apps/windows-app-sdk/). Similar to OpenAI's GPT Large Language Model (LLM) that powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices with a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a highly performant hardware-accelerated way directly on the device.

- [**Text Recognition with OCR**](../apis/text-recognition.md): **Not yet available.** The Text Recognition API (also referred to as Optical Character Recognition or OCR) is available as a part of the [Windows App SDK](/windows/apps/windows-app-sdk/). This API enables the recognition of text in an image and the conversion of different types of documents, such as scanned paper documents, PDF files or images captured by a digital camera, into editable and searchable data on a local device.

- [**Imaging APIs**](../apis/imaging.md): **Not yet available.** Windows will support a collection of AI-enhanced Imaging APIs available as part of Windows App SDK. These APIs perform a variety of actions such as intelligently scaling images and identifying objects within images.

- [**Studio Effects**](../studio-effects/index.md): **Available in Windows 11, version 22H2 or newer (Build 22623.885+), on Copilot+ PCs.** Windows devices with compatible Neural Processing Units (NPUs) integrate Studio Effects into the built-in device camera and microphone settings. Apply special effects that utilize AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, or Voice Focus for filtering out background noise.

- [**Recall**](../apis/recall.md): **Available for preview via Windows Insiders Program on Copilot+ PCs.** Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user’s Recall experience with their app by adding contextual information to the underlying vector database with the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app.

- **Live Caption Translations** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content, even when the audio content is in a language different from the system's preferred language.

## Use cloud-based AI-backed APIs in your Windows app

You also may be interested in using APIs that run models in the cloud to power AI features that can be added to your Windows app. A few examples of cloud-based AI-backed APIs offered by Microsoft or OpenAI include:

- [**Add OpenAI chat completions to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/chatgpt-openai-winui3): A tutorial on how to integrate the cloud-based OpenAI ChatGPT completion capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Add DALL-E to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/dall-e-winui3): A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Create a recommendation app with .NET MAUI and ChatGPT**](../samples/tutorial-maui-ai.md): A tutorial on how to create a sample Recommendation app that integrates the cloud-based OpenAI ChatGPT completion capabilities into a .NET MAUI app.

- [**Add DALL-E to your .NET MAUI Windows desktop app**](../samples/dall-e-maui-windows.md):  A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a .NET MAUI app.

- [**Azure OpenAI Service**](/azure/ai-services/openai/): If you want your Windows app to access OpenAI models, such as GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, DALLE-3 or the Embeddings model series, with the added security and enterprise capabilities of Azure, you can find guidance in this Azure OpenAI documentation.

- [**Azure AI Services**](/azure/ai-services/what-are-ai-services): Azure offers an entire suite of AI services available through REST APIs and client library SDKs in popular development languages. For more information, see each service's documentation. These cloud-based services help developers and organizations rapidly create intelligent, cutting-edge, market-ready, and responsible applications with out-of-the-box and prebuilt and customizable APIs and models. Example applications include natural language processing for conversations, search, monitoring, translation, speech, vision, and decision-making.

## Considerations for using local versus cloud-based AI-backed APIs in your Windows app

When deciding between using an API in your Windows app that relies on running an ML model locally versus in the cloud, there are several advantages and disadvantages to consider.

- **Resource Availability**
  - **Local Device:** Running a model depends on the resources available on the device being used, including the CPU, GPU, NPU, memory, and storage capacity. This can be limiting if the device does not have high computational power or sufficient storage. Small Language Models (SLMs), like [Phi](../apis/phi-silica.md), are more ideal for use locally on a device.
  - **Cloud:** Cloud platforms, such as Azure, offer scalable resources. You can use as much computational power or storage as you need and only pay for what you use. Large Language Models (LLMs), like the [OpenAI language models](https://platform.openai.com/docs/models), require more resources, but are also more powerful.

- **Data Privacy and Security**
  - **Local Device:** Since data remains on the device, running a model locally can be more secure and private. The responsibility of data security rests on the user.
  - **Cloud:** Cloud providers offer robust security measures, but data needs to be transferred to the cloud, which might raise data privacy concerns in some cases.

- **Accessibility and Collaboration**
  - **Local Device:** The model and data are accessible only on the device unless shared manually. This has the potential to make collaboration on model data more challenging.
  - **Cloud:** The model and data can be accessed from anywhere with internet connectivity. This may be better for collaboration scenarios.

- **Cost**
  - **Local Device:** There is no additional cost beyond the initial investment in the device.
  - **Cloud:** While cloud platforms operate on a pay-as-you-go model, costs can accumulate based on the resources used and the duration of usage.

- **Maintenance and Updates**
  - **Local Device:** The user is responsible for maintaining the system and installing updates.
  - **Cloud:** Maintenance, system updates, and new feature updates are handled by the cloud service provider, reducing maintenance overhead for the user.

See [Running a Small Language Model locally versus a Large Language Model in the cloud](../models.md#running-a-small-language-model-locally-versus-a-large-language-model-in-the-cloud) to learn more about the differences between running a Small Language Model (SLM) locally versus running a Large Language Model (LLM) in the cloud.
