---
title: Windows Copilot Runtime overview
description: Learn how to add the AI-backed Windows Copilot Runtime APIs to your Windows app.
ms.author: mattwoj
author: mattwojo
ms.date: 01/17/2025
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
---

# Windows Copilot Runtime overview

**Windows Copilot Runtime** offers a variety of AI-backed features and APIs that let you to tap into AI functionality without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows Copilot Runtime on Copilot+ PCs run locally and in the background at all times.

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

## Windows Copilot Runtime features and APIs for Windows apps

Windows Copilot Runtime includes the following features and AI-backed APIs (in the [Windows App SDK](/windows/apps/windows-app-sdk/)) powered by models running locally on the Windows device.

- [**Phi Silica**](../apis/phi-silica.md): **Not yet available.** The Phi Silica APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). Similar to OpenAI's GPT Large Language Model (LLM) that powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices with a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device.

- [**Text Recognition with OCR**](../apis/text-recognition.md): **Not yet available.** The Text Recognition APIs (also referred to as Optical Character Recognition or OCR) will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs enable the recognition of text in an image and the conversion of different types of documents (such as scanned paper documents, PDF files, or images captured by a digital camera) into editable and searchable data on a local device.

- [**Imaging APIs**](../apis/imaging.md): **Not yet available.** The AI-enhanced Imaging APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs perform a variety of actions such as intelligently scaling images and identifying objects within images.

- [**Studio Effects**](../studio-effects/index.md): **Available in Windows 11, version 22H2 or newer (Build 22623.885+), on Copilot+ PCs.** Windows devices with compatible Neural Processing Units (NPUs) integrate Studio Effects into the built-in device camera and microphone settings. Apply special effects that utilize AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, or Voice Focus for filtering out background noise.

- [**Recall**](../apis/recall.md): **Available for preview via Windows Insiders Program on Copilot+ PCs.** Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user's Recall experience with their app by adding contextual information to the underlying vector database with the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app.

- **Live Caption Translations** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content (even when the audio content is in a language different from the system's preferred language).

- [**Content Moderation with Windows Copilot Runtime**](./content-moderation.md): 

## Integrate AI in enterprise apps using Windows Copilot Runtime APIs

Watch the demo session [Integrate AI in Enterprise apps using Windows Copilot Runtime APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s) from the November 2024 Ignite Conference.

## Additional resources

- [Get started with AI on Windows](../overview.md): Windows Copilot Runtime implements a Text Content Moderation API to flag and filter out potentially harmful content. Learn more about this feature and how to adjust the filter sensitivity.
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md): Guidance for responsibly developing apps that incorporate AI.
- [AI on Windows Sample Gallery](../samples/index.md): A collection of samples that demonstrate a variety of ways to enhance your Windows apps using AI.
