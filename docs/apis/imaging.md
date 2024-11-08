---
title: Get Started with imaging in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) imaging features that will ship with the Windows App SDK and can be used to both scale and sharpen images as well as identify objects within an image.
ms.topic: article
ms.date: 11/08/2024
ms.author: kbridge
author: karl-bridge-microsoft
dev_langs:
- csharp
- cpp
---

# Get Started with imaging in the Windows App SDK

Imaging features will be supported by the [Windows App SDK](/windows/apps/windows-app-sdk/) through a set of artificial intelligence (AI)-backed APIs that can both scale and sharpen images (Image Super Resolution) as well as identify objects within an image (Image Segmentation).

For API details, see [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md).

> [!IMPORTANT]
> The Windows App SDK [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. They are not supported for use in production environments, and apps that use experimental features cannot be published to the Microsoft Store.

## Prerequisites

- [CoPilot+ PCs](/windows/ai/npu-devices/) containing a Qualcomm Snapdragon&reg; X Elite processor.

## What can I do with the Windows App SDK and Image Super Resolution?

Use the new AI Image Super Resolution features in the Windows App SDK to sharpen images and sharpen images while scaling them.

> [!NOTE]
> Scaling is limted to a maximum factor of 8x (higher scale factors can introduce artifacts and compromise image accuracy). If either the final width or height is greater than 8x their original values, an exception is thrown.

### Increase fidelity of an image

This example shows how to increase the scale of an image and improve its fidelity. A scale factor of 1 can be used if you don't want to improve the image fidelity.

1. First, we ensure the Image Super Resolution model is available by calling the IsAvailable method and waiting for the MakeAvailableAsync method to return successfully.
1. Once the Image Super Resolution model is available, we create an object to reference it.
1. Finally, we get the final image by submitting the original image and desired scale factor (use 1 if you don't want to scale the image) to the model using the ScaleImageBuffer method (or ScaleSoftwareBitmap depending on what object your image is stored as).

```csharp
if (!ImageScaler.IsAvailable())
{
    var result = ImageScaler.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}
imageScaler = await ImageScaler.CreateAsync();
var finalImage = imageScaler.ScaleSoftwareBitmap(inputBitmap, width, height);
```

## What can I do with the Windows App SDK and Image Segmentation?

Image Segmentation can be used to identify objects in an image.

The model takes both an image and a "hints" object to help the model more accurately find what you want to identify in the image. Hints can be provided in the form of coordinates that belong to what you're identifying, points that don't belong to what you're identifying, or a coordinate rectangle that encloses what you're identifying (while supported, multiple hint rectangles may produce worse results). You can use any combination of these types of hints to inform the model (the more hints you provide, the more precise the model can be).

> [!NOTE]
> A maximum of of 32 coordinates are supported (1 for each point, 2 for each rectangle).

### Identify an object within an image

The following examples show how the various ways you can identify an object within an image.

1. First, we ensure the Image Segmentation model is available by calling the IsAvailable method and waiting for the MakeAvailableAsync method to return successfully.
1. Once the Image Segmentation model is available, we create an object to reference it.
1. Next, we create an ImageObjectExtractor class by passing the image to ImageObjectExtractor.CreateWithImageBufferAsync (or CreateWithSoftwareBitmapAsync depending on your image type).
1. Then we'll create an ExctractorHint object.
1. Finally, we submit the image to the model using the GetImageBufferObjectMask method (or GetSoftwareBitmapObjectMask), which returns the final result.

```csharp
if (!ImageObjectExtractor.IsAvailable())
{
    var result = await ImageObjectExtractor.MakeAvailableAsync();
    if (result.Status != PackageDeploymentStatus.CompletedSuccess)
    {
        throw result.ExtendedError;
    }
}
ImageObjectExtractor imageObjectExtractor = await ImageObjectExtractor.CreateWithSoftwareBitmapAsync(bitmap);
```

#### Specify hints with included and excluded points

In this code snippet, we demonstrate how to use both included and excluded points as hints.

```csharp
ImageObjectExtractorHint hint1(includeRects: null,
    includePoints: new List<PointInt32> { new PointInt32(150, 90), new PointInt32(216, 336), new PointInt32(550, 330) },
    excludePoints: new List<PointInt32> { new PointInt32(306, 212) });
imageObjectExtractor.GetSoftwareBitmapObjectMask(null); 
```

#### Specify hints with rectangle

In this code snippet, we demonstrate how to use a rectangle (RectInt32 is `X, Y, Width, Height`) as a hint.

```csharp
ImageObjectExtractorHint hint2(includeRects: new List<RectInt32> { new RectInt32((370, 278, 285, 126) },
    includePoints: null,
    excludePoints: null ); 
```


## Responsible AI

The imaging features in the Windows App SDK provide developers with powerful, trustworthy models for building apps with safe, secure AI experiences. The following steps have been taken to ensure these models are trustworthy, secure, and built responsibly.

- Thorough testing and evaluation of the model quality to identify and mitigate potential risks.
- Creation of [model cards](https://huggingface.co/docs/hub/model-cards) that describe the strengths and limitations of each model and provide clarity about intended uses.
- Incremental roll out of experimental releases. Following the final experimental imaging release, the roll out will expand to signed apps to ensure that malware scans have been applied to applications with local model capabilities.
- Provide customer controls through the Capability Access Manager in Settings so users can turn off the model on the device for the system, user, or app.

## Additional resources

## Related content

- [API ref for AI-backed imaging features in the Windows App SDK](imaging-api-ref.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
