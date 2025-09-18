---
title: What are Windows AI APIs?
description: Windows AI Foundry provides a variety of AI-powered features, including Windows AI APIs and Windows ML.
ms.topic: article
ms.date: 09/17/2025
no-loc: [API, APIs, AI Dev Gallery, Windows Studio Effects, Recall, Windows AI Foundry]
dev_langs:
- csharp
- cpp
---

# What are Windows AI APIs?

:::image type="content" source="../images/ai-api-header.png" border="false" alt-text="Image showing the icons for various Windows AI Foundry APIs.":::

Windows AI Foundry provides a variety of artificial intelligence (AI) features through a suite of Windows AI APIs and hardware-abstracted AI inferencing capabilities enabled through Windows machine learning (ML). The Windows AI APIs enable AI capabilities without the need to find, run, or optimize your own machine learning (ML) model. The models that power Windows AI Foundry on Copilot+ PCs run locally and continuously in the background.

See the [WindowsAIFoundry WinUI sample app](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry/cs-winui) for how to use the Windows AI Foundry with WinUI.

> [!IMPORTANT]
> The following is a list of Windows AI features and the Windows App SDK release in which they are currently supported. See [Overview of available APIs](#overview-of-available-apis) later in this topic for brief descriptions.
>
> [**Version 1.8.0 (1.8.250907003)**](/windows/apps/windows-app-sdk/stable-channel#version-18) - [Phi Silica (Limited Access Feature)](phi-silica.md), [Conversation Summarization (Text Intelligence)](phi-silica.md#text-intelligence-skills), [Object Erase](imaging.md#what-can-i-do-with-object-erase)
>
> [**Version 1.8 Preview (1.8.0-preview)**](/windows/apps/windows-app-sdk/preview-channel#version-18-preview-18-preview) - [LoRA fine-tuning for Phi Silica](phi-silica-lora.md), [Text Rewriter Tone (Text Intelligence)](phi-silica.md#text-intelligence-skills)
>
> [**Private preview**](https://aka.ms/WindowsAIFSemanticSearch) - Semantic Search
>
> [**Version 1.7.1 (1.7.250401001)**](/windows/apps/windows-app-sdk/stable-channel#version-171-17250401001) - All other APIs
>
> These APIs will only be functional on Windows Insider Preview (WIP) devices that have received the May 7th update. On May 28-29, an optional update will be released to non-WIP devices, followed by the Jun 10 update. This update will bring with it the AI models required for the Windows AI APIs to function. These updates will also require that any app using Windows AI APIs will be unable to do so until the app has been granted package identity at runtime.

## Build your first AI-powered Windows app

> [!TIP]
> To improve accessibility and readability, this page displays still images by default. In some cases, you can click an image to see an animated version.

To build your first Windows app with Visual Studio and some simple Windows AI APIs, just meet the prerequisites and use the provided example code in [Get started building an app with Windows AI APIs](./get-started.md).

From there, you can jump into short tutorials that build an app leveraging specific Windows AI APIs such as the [Phi Silica walthrough](./phi-silica-tutorial.md), [Imaging walthrough](./imaging-tutorial.md) and [OCR walthrough](./text-recognition-tutorial.md).

## Try the APIs and models on your PC

AI Dev Gallery is a demo app&mdash;available from the Microsoft Store&mdash;that lets you quickly download, try out, and use Windows AI APIs and models.

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

In AI Dev Gallery, select the **Windows AI APIs tab** menu item, then select the *Phi Silica* sample. If the model is already available on your device, then that sample will run straight away. Otherwise, select **Request model** to download the model. Once downloaded, that sample will be activated. Learn more about the AI Dev Gallery in [What is the AI Dev Gallery?](../ai-dev-gallery/index.md).

## Overview of available APIs

Here are a few ready-to-use AI features that you can tap into from your Windows app:

### Phi Silica

Similar to OpenAI's GPT Large Language Model (LLM), which powers ChatGPT, Phi Silica is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device (see [Get started with Phi Silica](./phi-silica.md)). Phi Silica is specifically designed for Windows devices that have a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device. *Phi Silica is not available in China.*

:::image type="content" source="../images/waif-phisilica.png"  lightbox="../images/waif-phisilica.gif" alt-text="An animated gif showing an AI chat prompt reading introduce yourself and a response being generated using the Phi Silica feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/9b56e116-c142-4be1-827c-cb023743aca2?src=docs)

### Text recognition

The text recognition APIs enable the recognition of text in an image, and the conversion on a local device of different types of documents (such as scanned paper documents, PDF files, and images captured by a digital camera) into editable and searchable data (see [Get started with AI text recognition](./text-recognition.md)).

:::image type="content" source="../images/waif-ocr.png" lightbox="../images/waif-ocr.gif" alt-text="An animated gif showing words in a screenshot being recognized with text overlays that can be copied to a file or clipboard using the text recognition feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/4bcc0137-0e9a-4eda-8096-b235fcb0e98b?src=docs)

### Imaging

Scale and sharpen images (Image Super Resolution), identify objects within an image (Image Segmentation), generate natural-language descriptions of images (Image Description), and remove objects from images (Object Erase). See [Get Started with AI imaging](./imaging.md).

#### Image Super Resolution

The Image Super Resolution APIs enable image sharpening and scaling.

:::image type="content" source="../images/waif-superres.png" lightbox="../images/waif-superres.gif" alt-text="An animated gif showing an image with a mix of words and pictures that is being sharpened and scaled using the Image Super Resolution feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/97ed0b95-3f14-415c-bb1f-9a6c59b78c3d?src=docs)

Also see [What can I do with Image Super Resolution?](imaging.md#what-can-i-do-with-image-super-resolution).

#### Image Segmentation

The Image Segmentation APIs enable segmentation of images.

:::image type="content" source="../images/waif-backgroundremover.png" lightbox="../images/waif-backgroundremover.gif" alt-text="An animated gif showing a man lifting one foot off the ground, then selecting Remove Background to isolate the image of the man on a white background using the Image Segmentation feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/bdab049c-9b01-48f4-b12d-acb911b0a61c?src=docs)

Also see [What can I do with Image Segmentation?](imaging.md#what-can-i-do-with-image-segmentation).

#### Image Description

The Image Description APIs describes images in natural language.

> [!NOTE]
> Image Description features are not available in China.

:::image type="content" source="../images/waif-imagedescription.png" lightbox="../images/waif-imagedescription.gif" alt-text="An animated gif showing a sleeping dog that pops up a description of the image using natural language reading a fluffy, shaggy-haired dog lying down on a couch resting comfortably, using the Image Description feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/bdab049c-9b01-48f4-b12d-acb911b0a61c?src=docs)

Also see [Get text description from an image](imaging.md#get-text-description-from-an-image)

#### Object Erase

The Object Erase APIs allows for removing objects from images.

:::image type="content" source="../images/waif-objecterase.gif" lightbox="../images/waif-objecterase.gif" alt-text="An animated gif showing a an image where the user is removing objects from using the Object Erase feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/69637859-f91f-468a-99e0-9ed3fbc5f78e?src=docs)

Also see [Get started with Object Erase](../apis/imaging.md#what-can-i-do-with-object-erase)

### Additional AI features

- **Windows Studio Effects**. Windows devices that have compatible Neural Processing Units (NPUs) integrate Windows Studio Effects into the device's built-in camera and microphone settings. You can apply special effects that use AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, and Voice Focus for filtering out background noise. See [Windows Studio Effects Overview (Preview)](../studio-effects/index.md).

- [**Recall**](../apis/recall.md): Recall enables users to quickly find things from their past activity, such as documents, images, websites and more. Developers can enrich the user's Recall experience with their app by [adding support for relaunching content](../recall/recall-relaunch.md). This integration will help users pick up where they left off in your app, improving app engagement and user's seamless flow between Windows and your app. See [Recall overview](../recall/index.md).

- **Live Caption Translations (Not yet supported)**. Help everyone using Windows&mdash;including those who are deaf or hard of hearing&mdash;better understand audio by viewing captions of spoken content (even when the audio content is in a language that's different from the system's preferred language).

## Content moderation

Learn how Windows AI Foundry moderates content, and how to adjust sensitivity filters. See [Content safety moderation with Windows AI Foundry](content-moderation.md).

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

## Additional resources

- [Code samples and tutorials](../samples/index.md). A collection of samples that demonstrate a variety of ways to use AI to enhance your Windows apps.
- [Integrate AI in enterprise apps using Windows AI Foundry APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s). Watch the demo session from the November 2024 Microsoft Ignite conference.
- Provide **feedback** on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [WindowsAIFoundry samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
