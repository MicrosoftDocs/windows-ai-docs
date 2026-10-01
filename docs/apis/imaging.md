---
title: Imaging overview for Windows AI APIs
description: Learn how Windows imaging APIs scale and sharpen images, generate descriptions, and identify or remove objects by using on-device models.
ms.topic: overview
ms.date: 10/01/2026
---

# Imaging overview

> [!NOTE]
> The imaging APIs (Image Super Resolution, Image Description, Image Segmentation, Image Foreground Extraction, and Object Erase) currently require a **Copilot+ PC** with an NPU. For Video Super Resolution, which supports additional hardware, see [Get started with video super resolution](video-super-resolution.md). For a full overview of hardware support across all Windows AI APIs, see the [supported hardware table](index.md#supported-hardware).

The imaging features supported by the Windows AI APIs enable the following capabilities:

| Capability | Description |
|------------|-------------|
| [**Image Super Resolution**](image-super-resolution.md) | Scale and sharpen images up to 8x their original size while maintaining quality. |
| [**Image Description**](image-description.md) | Generate text descriptions for images, including brief, detailed, diagram, and accessible formats. |
| [**Image Object Extractor**](image-object-extractor.md) | Identify specific objects in an image using point and rectangle hints, returning a mask of the identified object. |
| [**Image Foreground Extractor**](image-foreground-extractor.md) | Segment the foreground of an image for background removal or sticker generation. |
| [**Image Object Erase**](image-object-erase.md) | Remove objects from images and replace the erased area with the image background. |

> [!div class="button"]
> [Try image super resolution in AI Dev Gallery](aidevgallery://apis/97ed0b95-3f14-415c-bb1f-9a6c59b78c3d?src=docs)

AI Dev Gallery includes interactive samples for the imaging capabilities on this page. You can inspect the C# source for each sample and export it as a Visual Studio project.

For **API details**, see [API ref for AI imaging features](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.imaging).

For **content moderation details**, see [Content safety with generative AI APIs](content-moderation.md).

## Responsible AI

Follow responsible AI recommendations, including transparency and user trust, when using these APIs to modify or generate images in your Windows apps. To help users understand the origin and history of generated or modified images, provide Content Credentials as specified by the [Coalition for Content Provenance and Authenticity (C2PA)](https://c2pa.org/) standards.

See [Responsible Generative AI Development on Windows](/windows/ai/rai) for best practices when adding model-powered imaging features to Windows apps.

## In this section

| Article | Description |
|---------|-------------|
| [Image Super Resolution](image-super-resolution.md) | Learn how to scale and sharpen images. |
| [Image Description](image-description.md) | Learn how to generate text descriptions for images. |
| [Image Object Extractor](image-object-extractor.md) | Learn how to identify objects in images using hints. |
| [Image Foreground Extractor](image-foreground-extractor.md) | Learn how to extract foreground from images. |
| [Image Object Erase](image-object-erase.md) | Learn how to remove objects from images. |

## See also

- [Explore and export samples with AI Dev Gallery](../ai-dev-gallery/index.md)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
