---
title: Windows Copilot Runtime Overview
description: Guidance to help developers get started using the AI features, tools, and capabilities available on Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 09/12/2024
ms.topic: overview
no-loc: [Windows Copilot Runtime, Windows Copilot Library, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# Windows Copilot Runtime Overview

**Windows Copilot Runtime** introduces new ways of interacting with the operating system that utilize AI, such as Phi Silica, the Small Language Model (SLM) created by Microsoft Research that is able to offer many of the same capabilities found in Large Language Models (LLMs), but more compact and efficient so that it can run locally on Windows.

As a developer, you can integrate your apps with AI-powered Windows experiences such as [Recall](./apis/recall.md) and [Studio Effects](./studio-effects/index.md), leverage new APIs powered by on-device models through the [Windows Copilot Library](./apis/index.md), discover Machine Learning (ML) models to fine-tune with your own customized data using [AI Toolkit in Visual Studio Code](./toolkit/index.md), integrate your own ML models using frameworks like ONNX Runtime, PyTorch, or WebNN, and access hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md).

> [!NOTE]
> Not all features are available at this time. Please follow Microsoft developer social channels and community events for the latest information on availability.


## Windows Copilot Runtime and Windows Copilot Library

There are new innovations that use AI to improve and redefine the Windows experience, with some AI innovations baked right into using Windows and others available for App Developers to integrate into their app features. These new ways of integrating AI into a Windows app form the **Windows Copilot Library**, a list of ready-to-use AI-backed features and APIs, including:

- [Studio Effects](./studio-effects/index.md): Enhances the camera and audio capabilities of Windows devices with AI-powered background effects, eye contact correction, automatic framing, voice focus, blur, lighting, and creative filters run on the device NPU to maintain fast performance speeds.
- [Recall](./apis/recall.md): Makes past activities on your Windows device searchable, so that you can pick up where you left off, whether that was using an app, editing a document, or responding to an email.
- [Phi Silica](./apis/phi-silica.md): Enables your app connect with the on-device Phi model for natural language processing tasks (chat, math, code, reasoning) using the Windows App SDK.
- [Text Recognition](./apis/text-recognition.md): Optical Character Recognition, or OCR, enables the extraction of text from images and documents. Imagine tasks like converting a PDF, paper document, or picture of a classroom white board into editable digital text.
- Live Caption Translations: Helps everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content, even when the audio content is in a language different from the system's preferred language.

<!-- Additional APIs and features to enhance developers’ ability to use AI in Windows will include:

- Semantic Search: An advanced search feature that utilizes AI to enhance the search capabilities within Windows.
- Retrieval Augmented Generation (RAG): Combining responses from the on-device model with an external knowledge retrieval system for more accurate and contextually relevant replies that can utilize real-time data or specific info from documents, databases, or the web.
- Text Summarization: Condense large amounts of text into shorter, focused summaries using AI.
- Image Super Resolution: Using AI to enhance the resolution and quality of images. -->

Developers will be able to access these APIs as part of the [Windows App SDK](/windows/apps/windows-app-sdk/).

In addition to the ready-to-use AI-backed APIs in Windows Copilot Library, we have guidance on how to enhance your app using Machine Learning (ML) models. This covers topics like:

- [How can Windows applications leverage ML models?](models.md#how-can-windows-applications-leverage-ml-models)
- [Where can I find open source ML models on the web?](models.md#find-open-source-ml-models-on-the-web)
- [How do I optimize an ML model to use in my Windows app?](models.md#how-do-i-optimize-an-ml-model-to-run-on-windows)
- [How can I fine-tune an ML model with my own custom data?](models.md#how-do-i-fine-tune-an-ml-model-with-my-customized-data-to-run-on-windows)
- [How can I leverage hardware acceleration for better performance with AI features?](models.md#how-can-i-leverage-hardware-acceleration-for-better-performance-with-ai-features)

## Leading with responsibility and examples

We have also created resources to support our developers seeking to integrate AI into your Windows applications by providing a Samples Gallery, a guide for Responsible AI use, and some high-level FAQs that can help with unpacking some of the terminology and concepts.

- [AI on Windows Sample Gallery](./samples/index.md): Samples demonstrating ways to integrate AI into Windows apps.

- [Developing Responsible Generative AI Applications and Features on Windows](rai.md): Resources and guidance to assist you in working with AI on Windows and incorporating AI responsibly into your Windows apps.

- [FAQs about using AI with Windows](faq.yml): Frequently asked questions about terminology and concepts related to using AI in a Windows context, covering questions like "What is DirectML?", "What is ONNX?", "What is ORT?", "What is an NPU?", "What is an SLM?", "What is inferencing?", "What is fine-tuning?", etc.

## Get started adding models to your Windows app

- [Get started with Phi3 and other language models in your Windows app with OnnxRuntime Generative AI](./models/get-started-models-genai.md)
- [Get started with ONNX models in your WinUI app with ONNX Runtime](./models/get-started-onnx-winui.md)
