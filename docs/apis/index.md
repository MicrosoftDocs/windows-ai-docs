---
title: Start Building an App with Windows AI APIs
description: Learn how to set up your development environment to build Windows AI APIs and check for model availability.
ms.topic: article
ms.date: 04/08/2025
ms.author: mattwoj
author: mousma
reviewer: raamleka
dev_langs:
- csharp
- cpp
---

# What are Windows AI APIs?

:::image type="content" source="../images/ai-api-header.png" border="false" alt-text="Header of Windows AI APIs":::

**Windows Copilot Runtime** provides a variety of AI-powered features including Windows AI APIs and Windows ML. The **Windows AI APIs** allow you to utilize AI capabilities without the need to find, run, or optimize your own Machine Learning (ML) model. The models that power Windows Copilot Runtime on Copilot+ PCs run locally and continuously in the background.

Here are a few common paths with varying complexity to **get started**:

## Easy: Try the APIs and models on your PC

AI Dev Gallery is an app that quickly let's you download, try out and use Windows AI APIs and models!

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

In AI Dev Gallery, select the **WCR API tab** menu item then select the *Phi Silica* sample. If the model is already available on your device, the sample should run straight away. If not, select on *request model* to download the model. Once downloaded, the sample will be activated. Learn more about the [AI Dev Gallery](../ai-dev-gallery/index.md).

## Medium: Build your first AI powered Windows app

Follow prerequisites and code snippets to build your first Windows app with Visual Studio and a simple Windows AI API.

> [!div class="button"]
> [Get Started with Windows AI APIs](get-started.md)

## Advanced: Dive into more complex tutorials

Jump into a step by step tutorial that adds OCR and Text Recognition capabilities in a Windows app.

> [!div class="button"]
> [Windows AI API Tutorial](api-tutorial.md)

Learn how to build and bring **your own models** by leveraging Windows ML.

> [!div class="button"]
> [Build your own models](index.md)

## Overview of Available APIs

### Phi Silica

Similar to OpenAI's GPT Large Language Model (LLM) that powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices with a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device. *Phi Silica is not available in mainland China.*

:::image type="content" source="../images/wcr-phisilica.gif" alt-text="An animated gif showing an AI chat prompt reading introduce yourself and a response being generated using the Phi Silica feature.":::

> [!div class="button"]
> [Try it out in AI Dev Gallery](index.md)

[**Get started developing with Phi Silica**](../apis/phi-silica.md)

### Text Recognition

The Text Recognition APIs enable the recognition of text in an image and the conversion of different types of documents (such as scanned paper documents, PDF files, or images captured by a digital camera) into editable and searchable data on a local device.

:::image type="content" source="../images/wcr-ocr.gif" alt-text="An animated gif showing words in a screenshot being recognized with text overlays that can be copied to a file or clipboard using the Text Recognition feature.":::

> [!div class="button"]
> [Try it out in AI Dev Gallery](index.md)

[**Get started developing  with Text Recognition**](../apis/text-recognition.md)

### Image Super Resolution

The Image Super Resolution APIs enable image sharpening and scaling.

:::image type="content" source="../images/wcr-superres.gif" alt-text="An animated gif showing an image with a mix of words and pictures that is being sharpened and scaled using the Image Super Resolution feature.":::

> [!div class="button"]
> [Try it out in AI Dev Gallery](index.md)

[**Get started developing with Image Super Resolution**](../apis/imaging.md#what-can-i-do-with-image-super-resolution)

### Image Segmentation

The Image Segmentation APIs enable segmentation of images.

:::image type="content" source="../images/wcr-backgroundremover.gif" alt-text="An animated gif showing a man lifting one foot off the ground, then selecting Remove Background to isolate the image of the man on a white background using the Image Segmentation feature.":::

> [!div class="button"]
> [Try it out in AI Dev Gallery](index.md)

[**Get started developing with Image Segmentation**](../apis/imaging.md#what-can-i-do-with-image-segmentation)

### Image Description

The Image Description APIs describes images in natural language. (*Image Description features are not available in mainland China.*)

:::image type="content" source="../images/wcr-imagedescription.gif" alt-text="An animated gif showing a sleeping dog that pops up a description of the image using natural language reading a fluffy, shaggy-haired dog lying down on a couch resting comfortably, using the Image Description feature.":::

> [!div class="button"]
> [Try it out in AI Dev Gallery](index.md)

[**Get started developing with Image Description**](../apis/imaging.md#get-text-description-from-an-image)

### Additional AI features

- [**Studio Effects**](../studio-effects/index.md): Windows devices with compatible Neural Processing Units (NPUs) integrate Studio Effects into the built-in device camera and microphone settings. Apply special effects that utilize AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, or Voice Focus for filtering out background noise.

- [**Recall**](../apis/recall.md) **(Not currently supported as an API)**:  Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user's Recall experience with their app by adding contextual information to the underlying vector database with the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app.

- **Live Caption Translations (Not yet supported)** help everyone on Windows, including those who are deaf or hard of hearing, better understand audio by viewing captions of spoken content (even when the audio content is in a language different from the system's preferred language).

## Additional resources

- [Get started with AI on Windows](../overview.md): Windows Copilot Runtime implements a Text Content Moderation API to flag and filter out potentially harmful content. Learn more about this feature and how to adjust the filter sensitivity.
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md): Guidance for responsibly developing apps that incorporate AI.
- [AI on Windows Sample Gallery](../samples/index.md): A collection of samples that demonstrate a variety of ways to enhance your Windows apps using AI.
- [Integrate AI in Enterprise apps using Windows Copilot Runtime APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s). Watch the demo session from the November 2024 Ignite Conference.

## Content Moderation

[**Content Moderation**](../apis/content-moderation.md): Learn how Windows Copilot Runtime moderates content and how to adjust sensitivity filters.

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).
