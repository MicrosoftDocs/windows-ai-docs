---
title: What are Windows AI APIs?
description: Windows Copilot Runtime provides a variety of AI-powered features, including Windows AI APIs and Windows ML.
ms.topic: article
ms.date: 04/17/2025
no-loc: [API, APIs, AI Dev Gallery, Windows Studio Effects, Recall, Windows Copilot Runtime]
dev_langs:
- csharp
- cpp
---

# What are Windows AI APIs?

> [!IMPORTANT]
> In the future, an app that uses WCR APIs will need to be granted package identity at runtime. For details of how to grant that, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app).

> [!NOTE]
> Currently, it's not possible to run an unpackaged or a self-contained app from the **Downloads** folder, or from anywhere under the `C:\Users` folder. For info about those terms, see [Advantages and disadvantages of packaging your app](/windows/apps/package-and-deploy/#advantages-and-disadvantages-of-packaging-your-app) and [Windows App SDK deployment overview](/windows/apps/package-and-deploy/deploy-overview).

:::image type="content" source="../images/ai-api-header.png" border="false" alt-text="Header of Windows AI APIs":::

Windows Copilot Runtime provides a variety of AI-powered features, including Windows AI APIs and Windows ML. The Windows AI APIs allow you to use AI capabilities without the need to either find, run, or optimize your own machine learning (ML) model. The models that power Windows Copilot Runtime on Copilot+ PCs run locally and continuously in the background.

### 📌 Video Placeholder: Insert video here

To get started, here are a few common paths with varying complexity.

## Easy: Try the APIs and models on your PC

AI Dev Gallery is a demo app&mdash;available from the Microsoft Store&mdash;that lets you quickly download, try out, and use Windows AI APIs and models.

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

In AI Dev Gallery, select the **WCR API tab** menu item, then select the *Phi Silica* sample. If the model is already available on your device, then that sample will run straight away. Otherwise, select **Request model** to download the model. Once downloaded, that sample will be activated. Learn more about the AI Dev Gallery in [What is the AI Dev Gallery?](../ai-dev-gallery/index.md).

## Intermediate: Build your first AI-powered Windows app

To build your first Windows app with Visual Studio and some simple Windows AI APIs, just meet the prerequisites and use the provided example code in [Get started building an app with Windows AI APIs](./get-started.md).

## Advanced: Dive into more complex tutorials

Jump into a step-by-step tutorial that adds OCR and text recognition capabilities to a Windows app in [Get Started with WCR APIs in a Windows app](./api-tutorial.md).

Learn how to build and bring your own models by leveraging Windows ML in [Tutorial: Bring your own AI model](./tutorials/hellowinai/index.yml).

## Overview of available APIs

Here are a few ready-to-use AI features that you can tap into from your Windows app:

* **Phi Silica**. A local, ready-to-use language model. See [Get started with Phi Silica](./phi-silica.md).
* **AI text recognition**. Recognize text in images, and convert images/pdfs into searchable text. See [Get started with AI text recognition](./text-recognition.md).
* **AI Imaging**. Scale and sharpen images using AI (Image Super Resolution), as well as identify objects within an image (Image Segmentation). See [Get Started with AI imaging](./imaging.md).
* **Windows Studio Effects**. Apply AI effects to your device's device's built-in camera and microphone. See [Windows Studio Effects overview](./studio-effects/index.md).

### Phi Silica

Similar to OpenAI's GPT Large Language Model (LLM), which powers ChatGPT, Phi is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device. Phi Silica is specifically designed for Windows devices that have a Neural Processing Unit (NPU), allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device. *Phi Silica is not available in China.*

:::image type="content" source="../images/wcr-phisilica.gif" alt-text="An animated gif showing an AI chat prompt reading introduce yourself and a response being generated using the Phi Silica feature.":::

You can try out Phi Silica in the AI Dev Gallery demo app:

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Also see [Get started with Phi Silica](./phi-silica.md).

### Text recognition

The text recognition APIs enable the recognition of text in an image, and the conversion on a local device of different types of documents (such as scanned paper documents, PDF files, and images captured by a digital camera) into editable and searchable data.

:::image type="content" source="../images/wcr-ocr.gif" alt-text="An animated gif showing words in a screenshot being recognized with text overlays that can be copied to a file or clipboard using the text recognition feature.":::

You can try out text recognition in the AI Dev Gallery demo app:

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Also see [Get started with AI text recognition](./text-recognition.md)

### Image Super Resolution

The Image Super Resolution APIs enable image sharpening and scaling.

:::image type="content" source="../images/wcr-superres.gif" alt-text="An animated gif showing an image with a mix of words and pictures that is being sharpened and scaled using the Image Super Resolution feature.":::

You can try out Image Super Resolution in the AI Dev Gallery demo app:

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Also see [What can I do with Image Super Resolution?](../imaging.md#what-can-i-do-with-image-super-resolution)

### Image Segmentation

The Image Segmentation APIs enable segmentation of images.

:::image type="content" source="../images/wcr-backgroundremover.gif" alt-text="An animated gif showing a man lifting one foot off the ground, then selecting Remove Background to isolate the image of the man on a white background using the Image Segmentation feature.":::

You can try out Image Segmentation in the AI Dev Gallery demo app:

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Also see [What can I do with Image Segmentation?](../imaging.md#what-can-i-do-with-image-segmentation)

### Image Description

The Image Description APIs describes images in natural language. *Image Description features are not available in China.*

:::image type="content" source="../images/wcr-imagedescription.gif" alt-text="An animated gif showing a sleeping dog that pops up a description of the image using natural language reading a fluffy, shaggy-haired dog lying down on a couch resting comfortably, using the Image Description feature.":::

You can try out Image Description in the AI Dev Gallery demo app:

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

Also see [Get text description from an image](../imaging.md#get-text-description-from-an-image)

### Additional AI features

* **Windows Studio Effects**. Windows devices that have compatible Neural Processing Units (NPUs) integrate Windows Studio Effects into the device's built-in camera and microphone settings. You can apply special effects that use AI, including: Background Blur, Eye Contact correction, Automatic Framing, Portrait Light correction, Creative Filters, and Voice Focus for filtering out background noise. See [Windows Studio Effects overview](./studio-effects/index.md).

* **Recall** **(Not currently supported as an API)**. Enables users to quickly find artifacts from their past activity, such as documents, images, websites, and more. As a developer, you can enrich your users' Recall experience with their app by adding contextual information to the underlying vector database by using the [User Activity API](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). That integration will help your users pick up where they left off in your app, thereby improving app engagement and users' seamless flow between Windows and your app. See [Recall overview](../recall.md).

* **Live Caption Translations (Not yet supported)**. Help everyone using Windows&mdash;including those who are deaf or hard of hearing&mdash;better understand audio by viewing captions of spoken content (even when the audio content is in a language that's different from the system's preferred language).

## Content moderation

* **Content Moderation**. Learn how Windows Copilot Runtime moderates content, and how to adjust sensitivity filters. See [Content safety moderation with Windows Copilot Runtime](../content-moderation.md).

When utilizing AI features, we recommend that you review: [Developing Responsible Generative AI Applications and Features on Windows](../rai.md).

## Additional resources

* [Develop responsible generative AI apps and features on Windows](../rai.md). Guidance for responsibly developing apps that incorporate AI.
* [Code samples and tutorials](../samples/index.md). A collection of samples that demonstrate a variety of ways to use AI to enhance your Windows apps.
* [Integrate AI in enterprise apps using Windows Copilot Runtime APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s). Watch the demo session from the November 2024 Microsoft Ignite conference.
