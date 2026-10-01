---
title: What are Windows AI APIs?
description: Windows AI APIs provide features backed by local machine learning models that run on supported Windows 11 devices and hardware.
ms.topic: article
ms.date: 10/01/2026
no-loc: [API, APIs, AI Dev Gallery, Recall, Microsoft Foundry on Windows]
dev_langs:
- csharp
- cpp
---

# What are Windows AI APIs?

:::image type="content" source="../images/ai-api-header.png" border="false" alt-text="Image showing the icons for various Windows AI APIs.":::

Windows AI Foundry provides a suite of Windows AI APIs and hardware-abstracted inferencing capabilities through Windows machine learning (ML). These APIs let you add supported features without finding, running, or optimizing your own machine learning model. The models that power Windows AI Foundry run locally on supported Windows 11 devices—including Copilot+ PCs with NPUs, devices with supported GPUs, and devices that meet the recommended CPU specifications—and can run continuously in the background.

## Supported hardware

Windows AI APIs are expanding beyond Copilot+ PCs to support a broader range of hardware. The following table shows the current hardware support for each API.

> [!NOTE]
> On a Copilot+ PC, supported APIs always run on the **NPU**. The **GPU** and **CPU** columns describe expansion to non-Copilot+ devices — they are not alternative backends you can opt into on a Copilot+ PC.

| API | NPU (Copilot+ PC) | GPU | CPU |
|---|---|---|---|
| [Phi Silica](phi-silica.md) | ✅ Available | ✅ Available ([NVIDIA and AMD](phi-silica.md#supported-hardware)) | ❌ Not supported |
| [Text Recognition (OCR)](text-recognition.md) | ✅ Available | ❌ Not supported | ❌ Not supported |
| [Speech Recognition](speech-recognition.md) | ✅ Available | ❌ Not supported | ✅ Available (optional, removable) |
| [Video Super Resolution](video-super-resolution.md) | ✅ Available | ❌ Not supported | ✅ Available |
| [Image Super Resolution](imaging.md) | ✅ Available | ❌ Not supported | ❌ Not supported |
| [Image Description](imaging.md) | ✅ Available | ❌ Not supported | ❌ Not supported |
| [Image Segmentation](imaging.md) | ✅ Available | ❌ Not supported | ❌ Not supported |
| [Object Erase](imaging.md) | ✅ Available | ❌ Not supported | ❌ Not supported |
| [Image Generation](image-generation.md) | ✅ Available (optional, removable) | ❌ Not supported | ❌ Not supported |

> [!NOTE]
> GPU support for Phi Silica is available on NVIDIA GeForce RTX 30 series and newer (6+ GB vRAM) and AMD Radeon RX 9060 series and newer (6+ GB vRAM). GPU inference requires **Developer Mode** to be enabled (**Settings** > **System** > **For developers**) and the latest GPU driver installed directly from the manufacturer (see [Phi Silica — GPU driver requirements](phi-silica.md#gpu-driver-requirements)). Video Super Resolution and Speech Recognition run on any CPU but perform best on devices that meet the **recommended specifications** (4 physical cores, 3 GHz or higher base clock, 32 MB or more of L3 cache). See the individual API pages for details and a runtime check.

### Model availability

The way the underlying AI model reaches a device depends on the API:

- **Phi Silica** — On Copilot+ PCs the model is **preinstalled** on the NPU. On GPU and CPU devices the model is **not** preinstalled — it is downloaded on demand the first time your app calls `EnsureReadyAsync`. Downloads can be several GB and run in the background through Windows Update. End users can remove or reinstall the model at **Settings** > **System** > **AI Components**. Apps should check `GetReadyState` first and show a consent dialog before triggering the download. See [Phi Silica — Model availability and download](phi-silica.md#model-availability-and-download) for the recommended UX pattern.
- **Image Generation** — Runs on the NPU only, but the model is **not preinstalled** because of its install size. It is downloaded on demand the first time your app calls `EnsureReadyAsync`, and users can later remove it at **Settings** > **System** > **AI Components**. Apps should check `GetReadyState` first and show a consent dialog before triggering the download. See [Image Generation — Model availability and download](image-generation.md#model-availability-and-download) for the recommended UX pattern.
- **Video Super Resolution** — The VSR model ships with the Windows App SDK on every supported hardware path. There is no first-run download, consent step, or removable model. See [Video Super Resolution — Recommended CPU specifications](video-super-resolution.md#recommended-cpu-specifications).
- **Speech Recognition** — On Copilot+ PCs the model is **preinstalled** on the NPU. On CPU-only devices the model is **not** preinstalled — it is downloaded on demand the first time your app calls `EnsureReadyAsync`, and users can later remove it at **Settings** > **System** > **AI Components**. Apps should check `GetReadyState` first and show a consent dialog before triggering the download on CPU. See [Speech Recognition — Model availability and download](speech-recognition.md#model-availability-and-download) for the recommended UX pattern.

See the [Windows AI APIs with WinUI sample app](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry/cs-winui) for how to use Microsoft Foundry on Windows with WinUI.

> [!IMPORTANT]
> The following is a list of Windows AI features and the Windows App SDK release in which they are currently supported. See [Overview of available APIs](#overview-of-available-apis) later in this topic for brief descriptions.
>
> [**Version 2.2.2-experimental9 (June 2026 Experimental)**] - [Phi Silica on GPU](phi-silica.md) (requires Windows Insider Experimental Channel build)
>
> [**Version 1.8.0 (1.8.250907003)**](/windows/apps/windows-app-sdk/stable-channel) - [Phi Silica (Limited Access Feature)](phi-silica.md), [Conversation Summarization (Text Intelligence)](phi-silica.md#text-intelligence-skills), [Object Erase](./image-object-erase.md)
>
> [**Version 1.8 Preview (1.8.0-preview)**](/windows/apps/windows-app-sdk/preview-channel) - [LoRA fine-tuning for Phi Silica](phi-silica-lora.md), [Text Rewriter Tone (Text Intelligence)](phi-silica.md#text-intelligence-skills)
>
> [**Private preview**](https://aka.ms/WindowsAIFSemanticSearch) - Semantic Search
>
> [**Version 1.7.1 (1.7.250401001)**](/windows/apps/windows-app-sdk/downloads) - All other APIs

## Build your first app with Windows AI APIs

> [!TIP]
> To improve accessibility and readability, this page displays still images by default. In some cases, you can click an image to see an animated version.

To build your first Windows app with these APIs, review the prerequisites and follow the example code in [Get started building an app with Windows AI APIs](./get-started.md).

You can then follow focused tutorials for the [Phi Silica walkthrough](./phi-silica-tutorial.md), [imaging walkthrough](./imaging-tutorial.md), and [text recognition walkthrough](./text-recognition-tutorial.md).

## Try the APIs and models on your PC

AI Dev Gallery is a demo app&mdash;available from the Microsoft Store&mdash;that lets you quickly download, try out, and use Windows AI APIs and models.

> [!div class="nextstepaction"]
> [Install AI Dev Gallery from the Microsoft Store](ms-windows-store://pdp/?productid=9N9PN1MM3BD5)

In AI Dev Gallery, select the **Windows AI APIs tab** menu item, then select the *Phi Silica* sample. If the model is already available on your device, then that sample will run straight away. Otherwise, select **Request model** to download the model. Once downloaded, that sample will be activated. Learn more about the AI Dev Gallery in [What is the AI Dev Gallery?](../ai-dev-gallery/index.md).

## Overview of available APIs

Here are some ready-to-use capabilities that you can add to your Windows app:

### Phi Silica

> [!IMPORTANT]
> **Phi Silica is being replaced by Aion Instruct**, a new on-device model. Aion Instruct begins rolling out to Windows Insider Preview devices in October 2026 and to retail devices in November 2026, at which point Phi Silica will be removed. See [Get started with Phi Silica](./phi-silica.md) for transition details and timeline.

Similar to Large Language Models (LLM), Phi Silica is a Small Language Model (SLM) developed by Microsoft Research to perform language-processing tasks on a local device (see [Get started with Phi Silica](./phi-silica.md)). Phi Silica is designed for Windows devices with a Neural Processing Unit (NPU) or a supported GPU, allowing text generation and conversation features to run in a high performance, hardware-accelerated way directly on the device. *Phi Silica is not available in China.*

:::image type="content" source="../images/waif-phisilica.png"  lightbox="../images/waif-phisilica.gif" alt-text="An animated gif showing an AI chat prompt reading introduce yourself and a response being generated using the Phi Silica feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/9b56e116-c142-4be1-827c-cb023743aca2?src=docs)

### Text recognition

The text recognition APIs recognize text in images and convert scanned documents, PDF files, and camera images into editable and searchable data on the device. See [Get started with text recognition](./text-recognition.md).

:::image type="content" source="../images/waif-ocr.png" lightbox="../images/waif-ocr.gif" alt-text="An animated gif showing words in a screenshot being recognized with text overlays that can be copied to a file or clipboard using the text recognition feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/4bcc0137-0e9a-4eda-8096-b235fcb0e98b?src=docs)

### Imaging

Scale and sharpen images (Image Super Resolution), identify objects within an image (Image Object Extractor), generate natural-language descriptions of images (Image Description), and remove objects from images (Object Erase). See the [imaging overview](./imaging.md).

#### Image Super Resolution

The Image Super Resolution APIs enable image sharpening and scaling.

:::image type="content" source="../images/waif-superres.png" lightbox="../images/waif-superres.gif" alt-text="An animated gif showing an image with a mix of words and pictures that is being sharpened and scaled using the Image Super Resolution feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/97ed0b95-3f14-415c-bb1f-9a6c59b78c3d?src=docs)

Also see [Image Super Resolution](image-super-resolution.md).

#### Image Object Extractor

The Image Object Extractor APIs enable identifying objects within images.

:::image type="content" source="../images/waif-backgroundremover.png" lightbox="../images/waif-backgroundremover.gif" alt-text="An animated gif showing a man lifting one foot off the ground, then selecting Remove Background to isolate the image of the man on a white background using the Image Object Extractor feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/bdab049c-9b01-48f4-b12d-acb911b0a61c?src=docs)

Also see [Image Object Extractor](image-object-extractor.md).

#### Image Description

The Image Description APIs describes images in natural language.

> [!NOTE]
> Image Description features are not available in China.

:::image type="content" source="../images/waif-imagedescription.png" lightbox="../images/waif-imagedescription.gif" alt-text="An animated gif showing a sleeping dog that pops up a description of the image using natural language reading a fluffy, shaggy-haired dog lying down on a couch resting comfortably, using the Image Description feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/bdab049c-9b01-48f4-b12d-acb911b0a61c?src=docs)

Also see [Image Description](image-description.md)

#### Object Erase

You can use the Object Erase APIs to remove objects from images.

:::image type="content" source="../images/waif-objecterase.gif" lightbox="../images/waif-objecterase.gif" alt-text="An animated gif showing a an image where the user is removing objects from using the Object Erase feature.":::

> [!div class="button"]
> [Try it in AI Dev Gallery](aidevgallery://apis/69637859-f91f-468a-99e0-9ed3fbc5f78e?src=docs)

Also see [Object Erase](image-object-erase.md)

### Planned features

The following capabilities are planned but not yet available for use:

- **Live Translation (Not yet supported)**. Help everyone using Windows&mdash;including those who are deaf or hard of hearing&mdash;better understand audio by viewing captions of spoken content (even when the audio content is in a language that's different from the system's preferred language).

## Content moderation

Learn how content is moderated by the Windows AI APIs, and how you can adjust sensitivity filters. See [Content safety moderation with the Windows AI APIs](content-moderation.md).

When adding model-powered features, review [Responsible Generative AI Development on Windows](../rai.md).

## Additional resources

- [Code samples and tutorials](../samples/index.md). Explore ways to add local models and Windows APIs to your apps.
- [Integrate AI in enterprise apps using Windows AI APIs](https://www.youtube.com/watch?v=Ob_63Fv1cLI&t=79s). Watch the demo session from the November 2024 Microsoft Ignite conference.
- Provide **feedback** on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

## See also

- [Explore and export samples with AI Dev Gallery](../ai-dev-gallery/index.md)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
