---
title: Windows Copilot Runtime overview
description: Guidance to help developers get started using the AI features, tools, and capabilities available on Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 12/02/2024
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# Windows Copilot Runtime overview

**Windows Copilot Runtime** offers a variety of AI-backed features and APIs that let you to tap into AI functionality without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows Copilot Runtime on Copilot+ PCs run locally and in the background at all times.

## Windows Copilot Runtime features and APIs for Windows apps

Windows Copilot Runtime includes the following features and AI-backed APIs (in the [Windows App SDK](/windows/apps/windows-app-sdk/)) powered by models running locally on the Windows device.

- [**Phi Silica**](apis/phi-silica.md): **Not yet available.** The Phi Silica APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). Similar to OpenAI's GPT Large Language Model (LLM) that powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices with a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device.

- [**Text Recognition with OCR**](apis/text-recognition.md): **Not yet available.** The Text Recognition APIs (also referred to as Optical Character Recognition or OCR) will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs enable the recognition of text in an image and the conversion of different types of documents (such as scanned paper documents, PDF files, or images captured by a digital camera) into editable and searchable data on a local device.

- [**Imaging APIs**](apis/imaging.md): **Not yet available.** The AI-enhanced Imaging APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs perform a variety of actions such as intelligently scaling images and identifying objects within images.

- [**Studio Effects**](studio-effects/index.md): **Available in Windows 11, version 22H2 or newer (Build 22623.885+), on Copilot+ PCs.** Windows devices with compatible Neural Processing Units (NPUs) integrate Studio Effects into the built-in device camera and microphone settings. Apply special effects that utilize AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, or Voice Focus for filtering out background noise.

- [**Recall**](apis/recall.md): **Available for preview via Windows Insiders Program on Copilot+ PCs.** Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user's Recall experience with their app by adding contextual information to the underlying vector database with the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app.

- **Live Caption Translations** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content (even when the audio content is in a language different from the system's preferred language).

## Integrate your own ML models

In addition to the AI-backed Windows Copilot Runtime APIs, we have tools and guidance on how to enhance your app using Machine Learning (ML) models.

**[AI Toolkit in Visual Studio Code](./toolkit/index.md)** enables you to integrate your own ML models using frameworks like ONNX Runtime, PyTorch, or WebNN, and access hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md).

Learn more:

- [How can Windows applications leverage ML models?](models.md#how-can-windows-applications-leverage-ml-models)
- [Where can I find open source ML models on the web?](models.md#find-open-source-ml-models-on-the-web)
- [How do I optimize an ML model to use in my Windows app?](models.md#how-do-i-optimize-an-ml-model-to-run-on-windows)
- [How can I fine-tune an ML model with my own custom data?](models.md#how-do-i-fine-tune-an-ml-model-with-my-customized-data-to-run-on-windows)
- [How can I leverage hardware acceleration for better performance with AI features?](models.md#how-can-i-leverage-hardware-acceleration-for-better-performance-with-ai-features)

## Responsible AI practices

**Develop apps responsibly with AI** using Windows Copilot Runtime on-device generative AI models to help enforce local content safety features, such as on-device classification engines for harmful content and a default blocklist. Microsoft prioritizes supporting developers to build safe, trustworthy AI experiences with local models on Windows. Learn more about responsible development practices to apply as you create applications and AI-assisted features that run on Windows devices in the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance.

## See also

- [Phi Silica, small but mighty on-device SLM](https://blogs.windows.com/windowsexperience/2024/12/06/phi-silica-small-but-mighty-on-device-slm/) (Windows Blogs)
- [AI on Windows Sample Gallery](./samples/index.md): Samples demonstrating ways to integrate AI into Windows apps.

- [FAQs about using AI with Windows](faq.yml): Frequently asked questions about terminology and concepts related to using AI in a Windows context, covering questions like "What is DirectML?", "What is ONNX?", "What is ORT?", "What is an NPU?", "What is an SLM?", "What is inferencing?", "What is fine-tuning?", etc.

- [Get started with Phi3 and other language models in your Windows app with ONNX Runtime Generative AI](models/get-started-models-genai.md)

- [Get started with ONNX models in your WinUI app with ONNX Runtime](models/get-started-onnx-winui.md)
