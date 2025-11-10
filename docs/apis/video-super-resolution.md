---
title: Get Started with AI Video Super Resolution (SR) in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) video super resolution features that will ship with the Windows App SDK and can be used to up-sample video frames to reduce network bandwidth and latency while maintaining high-resolution image fidelity
ms.topic: get-started
ms.date: 11/06/2025
dev_langs:
- csharp
- cpp
---

# Get Started with AI Video Super Resolution (SR)

Poor network conditions may affect the quality of video streaming, video calls, and screen sharing. Super Resolution (SR) solves this problem by allowing video to be streamed over the network at lower resolution and using AI-based video up-sampling to generate full-resolution video frames.

## Input and output restrictions and performance


| Restriction | Value |
|-------------|-------|
| Input image size range | 240p – 1440p |
| Output image size range | 480p – 1440p |
| Input FPS range | 15 fps – 60 fps | 
| Maximum allocated time for frame processing range | Usually 1 / output FPS |
| Accuracy range: CMOS >= x | Input pixel format: RGB |
| Output pixel format | RGB |
| NPU TOPS range | >= 40 TOPS |
| Currently supported chipsets | Intel Lunar Lake, Qualcomm Cadmus, AMD Strix |

## Video Super Resolution sample

 The example code in this article is based on the Super Resolution component of the [WindowsAIFoundry sample](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)


## Create a VideoScaler session

```csharp
private VideoScaler? _session;

    private VideoScaler Session => _session ?? throw new InvalidOperationException("Video Scaler session was not created yet");

    public async Task CreateModelSessionWithProgress(IProgress<double> progress, CancellationToken cancellationToken = default)
    {
        var catalog = ExecutionProviderCatalog.GetDefault();
        await catalog.EnsureAndRegisterCertifiedAsync();

        progress.Report(0.5);

        if (VideoScaler.GetReadyState() == AIFeatureReadyState.NotReady)
        {
            var videoScalerDeploymentOperation = VideoScaler.EnsureReadyAsync();
            videoScalerDeploymentOperation.Progress = (_, modelDeploymentProgress) =>
            {
                progress.Report(0.5 + (modelDeploymentProgress * 0.25) % 0.25);  // all progress is within 50% and 75%
            };
            using var _ = cancellationToken.Register(() => videoScalerDeploymentOperation.Cancel());
            await videoScalerDeploymentOperation;
        }
        else
        {
            progress.Report(0.75);
        }
        _session = await VideoScaler.CreateAsync();
        progress.Report(1.0); // 100% progress
    }
```

## Scale a video frame


```csharp
    public SoftwareBitmap ScaleVideoFrame(SoftwareBitmap inputFrame)
    {
        ImageBuffer inputImageBuffer = SoftwareBitmapExtensions.ConvertToBgr8ImageBuffer(inputFrame);
        var size = (uint)(inputFrame.PixelWidth * inputFrame.PixelHeight * 3);
        IBuffer outputBuffer = new global::Windows.Storage.Streams.Buffer(size);
        outputBuffer.Length = size;
        ImageBuffer outputImageBuffer = ImageBuffer.CreateForBuffer(
            outputBuffer,
            ImageBufferPixelFormat.Bgr8,
            inputFrame.PixelWidth,
            inputFrame.PixelHeight,
            inputFrame.PixelWidth * 3);
        var result = Session.ScaleFrame(inputImageBuffer, outputImageBuffer, new VideoScalerOptions());
        if (result.Status != ScaleFrameStatus.Success)
        {
            throw new Exception($"Failed to scale video frame: {result.Status}");
        }

        return SoftwareBitmapExtensions.ConvertBgr8ImageBufferToBgra8SoftwareBitmap(outputImageBuffer);
    }
```

## Software bitmap extension methods

```csharp
public static ImageBuffer ConvertToBgr8ImageBuffer(SoftwareBitmap input)
    {
        var bgraBitmap = input;
        if (input.BitmapPixelFormat != BitmapPixelFormat.Bgra8)
        {
            bgraBitmap = SoftwareBitmap.Convert(input, BitmapPixelFormat.Bgra8, BitmapAlphaMode.Premultiplied);
        }

        int width = bgraBitmap.PixelWidth;
        int height = bgraBitmap.PixelHeight;

        byte[] bgraBuffer = new byte[width * height * 4];
        bgraBitmap.CopyToBuffer(bgraBuffer.AsBuffer());

        byte[] bgrBuffer = new byte[width * height * 3];
        for (int i = 0, j = 0; i < bgraBuffer.Length; i += 4, j += 3)
        {
            bgrBuffer[j] = bgraBuffer[i];
            bgrBuffer[j + 1] = bgraBuffer[i + 1];
            bgrBuffer[j + 2] = bgraBuffer[i + 2];
        }

        return ImageBuffer.CreateForBuffer(
            bgrBuffer.AsBuffer(),
            ImageBufferPixelFormat.Bgr8,
            width,
            height,
            width * 3);
    }

    public static SoftwareBitmap ConvertBgr8ImageBufferToBgra8SoftwareBitmap(ImageBuffer bgrImageBuffer)
    {
        if (bgrImageBuffer.PixelFormat != ImageBufferPixelFormat.Bgr8)
        {
            throw new ArgumentException("Input ImageBuffer must be in Bgr8 format");
        }

        int width = bgrImageBuffer.PixelWidth;
        int height = bgrImageBuffer.PixelHeight;

        // Get BGR data from ImageBuffer
        byte[] bgrBuffer = new byte[width * height * 3];
        bgrImageBuffer.CopyToByteArray(bgrBuffer);

        // Create BGRA buffer (4 bytes per pixel)
        byte[] bgraBuffer = new byte[width * height * 4];

        for (int i = 0, j = 0; i < bgrBuffer.Length; i += 3, j += 4)
        {
            bgraBuffer[j] = bgrBuffer[i];     // B
            bgraBuffer[j + 1] = bgrBuffer[i + 1]; // G
            bgraBuffer[j + 2] = bgrBuffer[i + 2]; // R
            bgraBuffer[j + 3] = 255;              // A (full opacity)
        }

        // Create SoftwareBitmap and copy data
        var softwareBitmap = new SoftwareBitmap(
            BitmapPixelFormat.Bgra8,
            width,
            height,
            BitmapAlphaMode.Premultiplied);

        softwareBitmap.CopyFromBuffer(bgraBuffer.AsBuffer());

        return softwareBitmap;
    }
```

## Responsible AI

We have used a combination of the following steps to ensure these imaging APIs are trustworthy, secure, and built responsibly. We recommend reviewing the best practices described in [Responsible Generative AI Development on Windows](../rai.md) when implementing AI features in your app.

These video super resolution APIs use Machine Learning (ML) models, were designed specifically for scenarios such as video calling and conferencing apps and social and short-form videos that feature human faces speaking. Therefore, we do not recommend using these APIs for images in the following scenarios:

* Where the images contain potentially sensitive content and inaccurate descriptions could be controversial, such as flags, maps, globes, cultural symbols, or religious symbols.
* When accurate descriptions are critical, such as for medical advice or diagnosis, legal content, or financial documents.