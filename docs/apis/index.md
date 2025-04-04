---
title: Windows Copilot Runtime overview
description: Learn how to add the AI-backed Windows Copilot Runtime APIs to your Windows app.
ms.author: mattwoj
author: mattwojo
ms.date: 02/19/2025
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
---

# Windows Copilot Runtime overview

> [!IMPORTANT]
> Self-contained apps are not supported.

**Windows Copilot Runtime** provides a variety of AI-powered features available via APIs, allowing you to utilize AI capabilities without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows Copilot Runtime on Copilot+ PCs run locally and continuously in the background.

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

### Windows Copilot Runtime APIs

Windows Copilot Runtime includes the following features and AI-backed APIs powered by models running locally on the Windows device. **These APIs are currently only available in the latest [experimental channel release of the Windows App SDK](/windows/apps/windows-app-sdk/experimental-channel).

To get started trying available APIs, see [Set up your development environment to build Windows Copilot Runtime APIs](model-setup.md), this guidance includes code to check whether the required models are available on the user's device.

The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

#### Phi Silica
The Phi Silica APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). Similar to OpenAI's GPT Large Language Model (LLM) that powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices with a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device. *Phi Silica is not available in mainland China.*

:::image type="content" source="../images/phisilica.gif" alt-text="An animated gif of a sample using Phi Silica":::

[**Get started with Phi Silica**](../apis/phi-silica.md): 

#### Text Recognition
The Text Recognition APIs (also referred to as Optical Character Recognition or OCR) will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs enable the recognition of text in an image and the conversion of different types of documents (such as scanned paper documents, PDF files, or images captured by a digital camera) into editable and searchable data on a local device. 

:::image type="content" source="/images/ocr.gif" alt-text="An animated gif of a sample that shows text recognition":::

[**Get started with Text Recognition**](../apis/text-recognition.md)

#### Imaging APIs
- [**Imaging APIs**](../apis/imaging.md): The AI-enhanced Imaging APIs will ship in the [Windows App SDK](/windows/apps/windows-app-sdk/). These APIs perform a variety of actions such as intelligently scaling images and identifying objects within images. *Image Description features are not available in mainland China.*

#### Content Moderation
[**Content Moderation**](../apis/content-moderation.md): Learn how Windows Copilot Runtime moderates content and how to adjust sensitivity filters.

#### Other features
- [**Studio Effects**](../studio-effects/index.md): Windows devices with compatible Neural Processing Units (NPUs) integrate Studio Effects into the built-in device camera and microphone settings. Apply special effects that utilize AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, or Voice Focus for filtering out background noise.

- [**Recall**](../apis/recall.md) **(Not currently supported as an API)**:  Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user's Recall experience with their app by adding contextual information to the underlying vector database with the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app.

- **Live Caption Translations (Not currently supported)** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content (even when the audio content is in a language different from the system's preferred language).

#### Integrate AI in enterprise apps using Windows Copilot Runtime APIs

Watch the demo session [Integrate AI in Enterprise apps using Windows Copilot Runtime APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s) from the November 2024 Ignite Conference.

#### Additional resources

- [Get started with AI on Windows](../overview.md): Windows Copilot Runtime implements a Text Content Moderation API to flag and filter out potentially harmful content. Learn more about this feature and how to adjust the filter sensitivity.
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md): Guidance for responsibly developing apps that incorporate AI.
- [AI on Windows Sample Gallery](../samples/index.md): A collection of samples that demonstrate a variety of ways to enhance your Windows apps using AI.
