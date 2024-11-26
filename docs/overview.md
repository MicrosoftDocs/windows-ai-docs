---
title: Windows Copilot Runtime Overview
description: Guidance to help developers get started using the AI features, tools, and capabilities available on Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 11/20/2024
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# Windows Copilot Runtime Overview

**Windows Copilot Runtime** is suite of tools that provides developers with the ability to create AI experiences on and for Windows, including new ways of interacting with the operating system.

The **[Windows Copilot Runtime APIs](/windows/ai/apis/)** enable Windows app developers to access AI features run locally on the operating system, without the need to find, run, or optimize your own Machine Learning (ML) model. Windows Copilot Runtime APIs are targeted for availability in the Windows App SDK 1.7 Experimental 2 release planned for January, 2025. Learn more about the [Windows App SDK](/windows/apps/windows-app-sdk/).

**[Phi Silica](./apis/phi-silica.md)** is included among these APIs. An on-device language model created by Microsoft Research, Phi Silica offers many of the same capabilities found in Large Language Models (LLMs), but more compact and efficient so that it can run locally on Windows.

**[Text Recognition](./apis/text-recognition.md)**, or Optical Character Recognition (OCR), which recognizes and extracts text present within an image, will be available as an API working in alignment with Phi Silica.

**[Imaging APIs](./apis/imaging.md)** are a collection of APIs available as part of Windows App SDK. These APIs perform a variety of actions such as intelligently scaling images and identifying the borders of objects within images.

**[Recall](./apis/recall.md)**, an AI-assisted search feature that makes past activities on your Windows device searchable, so that you can pick up where you left off, whether that was using an app, editing a document, or responding to an email.

**[Studio Effects](./studio-effects/index.md)** Enhances the camera and audio capabilities of Windows devices with compatible Neural Processing Units (NPUs) to apply AI-powered background effects, eye contact correction, automatic framing, voice focus, blur, lighting, and creative filters run on the device NPU to maintain fast performance speeds.

**Live Caption Translations** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content, even when the audio content is in a language different from the system's preferred language. 

## Integrate your own ML models

In addition to the AI-backed Windows Copilot Runtime APIs, we have tools and guidance on how to enhance your app using Machine Learning (ML) models.

**[AI Toolkit in Visual Studio Code](./toolkit/index.md)** enables you to integrate your own ML models using frameworks like ONNX Runtime, PyTorch, or WebNN, and access hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md).

Learn more:

- [How can Windows applications leverage ML models?](models.md#how-can-windows-applications-leverage-ml-models)
- [Where can I find open source ML models on the web?](models.md#find-open-source-ml-models-on-the-web)
- [How do I optimize an ML model to use in my Windows app?](models.md#how-do-i-optimize-an-ml-model-to-run-on-windows)
- [How can I fine-tune an ML model with my own custom data?](models.md#how-do-i-fine-tune-an-ml-model-with-my-customized-data-to-run-on-windows)
- [How can I leverage hardware acceleration for better performance with AI features?](models.md#how-can-i-leverage-hardware-acceleration-for-better-performance-with-ai-features)

## Responsible AI practices and samples

**Develop apps responsibly with AI** using the Windows Copilot Runtime on-device generative AI models to help enforce local content safety features, such as on-device classification engines for harmful content and a default blocklist. Microsoft prioritizes supporting developers to build safe, trustworthy AI experiences with local models on Windows. Learn more about responsible development practices to apply as you create applications and AI-assisted features that run on Windows devices in the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance.

Additional resources for developers interested in integrated AI-features or models with your Windows app, include:

- [AI on Windows Sample Gallery](./samples/index.md): Samples demonstrating ways to integrate AI into Windows apps.

- [FAQs about using AI with Windows](faq.yml): Frequently asked questions about terminology and concepts related to using AI in a Windows context, covering questions like "What is DirectML?", "What is ONNX?", "What is ORT?", "What is an NPU?", "What is an SLM?", "What is inferencing?", "What is fine-tuning?", etc.

- [Get started with Phi3 and other language models in your Windows app with OnnxRuntime Generative AI](./models/get-started-models-genai.md)

- [Get started with ONNX models in your WinUI app with ONNX Runtime](./models/get-started-onnx-winui.md)
