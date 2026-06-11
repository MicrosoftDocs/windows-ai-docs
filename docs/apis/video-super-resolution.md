---
title: Get Started with AI Video Super Resolution (VSR) in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) video super resolution features that will ship with the Windows App SDK that you can use to up-sample video frames to reduce network bandwidth and latency while maintaining high-resolution image fidelity
ms.topic: get-started
ms.date: 01/21/2026
dev_langs:
- csharp
- cpp
---

# Get Started with AI Video Super Resolution (VSR)

Video Super Resolution (VSR) is an AI-based video up-sampling technology that intelligently upscales low-resolution video streams of people, restoring sharpness and detail that would otherwise be lost due to bandwidth limitations, poor network conditions, compression, or lower-quality source content.

Adding VSR capabilities to your app enables scenarios including the following:

- Improving video quality over poor network connections
- Bandwidth optimization to reduce CDN costs
- High-bandwidth scenarios like group video calls with multiple participants
- Improving social media video quality in editing, upload or viewing

The VSR feature is available on **Copilot+ PCs** with an NPU and on devices that meet the **[recommended CPU specifications](#recommended-cpu-specifications)**. For more information, see [Develop AI applications for Copilot+ PCs](../npu-devices/index.md) and the [supported hardware table](index.md#supported-hardware).

## Supported hardware

| Hardware | Status | Details |
|---|---|---|
| NPU (Copilot+ PC) | ✅ Available | Best performance. See [Copilot+ PCs developer guide](../npu-devices/index.md). |
| CPU | ✅ Available | Best on devices meeting the [recommended CPU specifications](#recommended-cpu-specifications). VSR will still attempt to run on lower-spec devices, but real-time quality and frame rate may suffer. |
| GPU | ❌ Not supported | VSR is not available on GPU. |

> [!NOTE]
> **Hardware selection is automatic.** On a Copilot+ PC with an NPU, VSR always runs on the NPU. On non-Copilot+ devices, VSR runs on the CPU automatically — there is no developer or end-user opt-in to select CPU on a Copilot+ device. This matches the pattern used by other Windows AI APIs; see [Supported hardware](index.md#supported-hardware) for the cross-API view.

These VSR APIs use Machine Learning (ML) models, were designed specifically for scenarios such as video calling and conferencing apps and social and short-form videos that feature human faces speaking

VSR currently supports the following resolution, format, and FPS ranges:

| Attribute | Supported Content |
|-------------|-------|
| Input resolution| 240p – 1440p |
| Output resolution | 480p – 1440p |
| Frames-per-second (FPS) range | 15 fps – 60 fps |
| Input pixel format | BGR (ImageBuffer API), NV12 (Direct3D API) |
| Output pixel format | BGR (ImageBuffer API), BGRA (Direct3D API) |

## Recommended CPU specifications

The VSR model is delivered as part of the Windows App SDK, so there is no separate download or first-run consent step on CPU devices. VSR will run on any CPU that the rest of the Windows AI APIs run on, but real-time scaling quality and frame rate scale with the host CPU.

For a good CPU experience, target devices that meet **all** of the following recommended specifications:

- **4 or more physical cores**
- **3 GHz or higher base clock**
- **32 MB or more of L3 cache**

These are recommendations, not hard minimums — the API will still attempt to scale on lower-spec devices. Apps that target a broad CPU range should query the CPU at runtime and, if the device falls below the recommended bar, fall back to a non-VSR pipeline or surface a quality-tradeoff hint to the user.

> [!NOTE]
> `GetReadyState` and the CPU spec check answer **different** questions and should be used together. `GetReadyState` tells you whether VSR is supported on the device at all (model loaded, drivers present, hardware policy allows it). The CPU spec check tells you whether VSR will run *well enough* for your UX. Use `GetReadyState` to decide whether to call VSR; use the CPU check to decide between VSR and a lightweight fallback (such as bilinear upscaling) on borderline hardware.

### Check CPU support level

The following C# sample uses **Windows Management Instrumentation (WMI)** to query the `Win32_Processor` class and returns `true` when the device meets the recommended specs. The sample uses the Microsoft-published [`System.Management`](https://www.nuget.org/packages/System.Management) NuGet package — the standard .NET wrapper for WMI — to call into WMI from C#.

```csharp
using System;
using System.Management;

private static bool MeetsRecommendedCpuSpecs()
{
    const int RecommendedCores      = 4;
    const int RecommendedClockMhz   = 3000;
    const int RecommendedL3CacheKb  = 32 * 1024; // 32 MB

    int totalCores   = 0;
    int maxClockMhz  = 0;
    int maxL3CacheKb = 0;

    using var searcher = new ManagementObjectSearcher(
        "SELECT NumberOfCores, MaxClockSpeed, L3CacheSize FROM Win32_Processor");

    foreach (ManagementObject processor in searcher.Get())
    {
        totalCores   += Convert.ToInt32(processor["NumberOfCores"]);
        maxClockMhz   = Math.Max(maxClockMhz,   Convert.ToInt32(processor["MaxClockSpeed"]));
        maxL3CacheKb  = Math.Max(maxL3CacheKb,  Convert.ToInt32(processor["L3CacheSize"]));
    }

    return totalCores   >= RecommendedCores
        && maxClockMhz  >= RecommendedClockMhz
        && maxL3CacheKb >= RecommendedL3CacheKb;
}
```

Use the result to gate UX choices — for example, show a quality-tradeoff hint, default to a lower output resolution, or skip VSR entirely on devices that fall below the recommendations.

#### Notes on the WMI check

- **Cache the result.** The first WMI query takes ~50–200 ms because of COM initialization. Subsequent queries are fast, but the cleanest pattern is to run `MeetsRecommendedCpuSpecs` once at startup and cache the boolean for the lifetime of the process. CPU hardware doesn't change at runtime.
- **`MaxClockSpeed` is the rated base clock, not the boost clock.** A 2.5 GHz CPU that turbos to 4.5 GHz will report `2500` and fail the 3 GHz check. This is intentional — sustained throughput matters more than peak boost for streaming AI workloads like VSR, so the base clock is the right signal.
- **Alternative for sub-millisecond checks.** If you need to avoid the `System.Management` dependency or the WMI cost is unacceptable (for example, on a hot startup path), the same data is available through native Windows APIs: [`GetLogicalProcessorInformationEx`](/windows/win32/api/sysinfoapi/nf-sysinfoapi-getlogicalprocessorinformationex) returns core counts and cache sizes, and the rated clock speed is exposed at `HKLM\HARDWARE\DESCRIPTION\System\CentralProcessor\0` (`~MHz` value). These return in sub-millisecond time but require more code.

## Create a VideoScaler session

The following example shows how to create a VSR session. First, get an instance of [ExecutionProviderCatalog](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning.executionprovidercatalog) and call [EnsureAndRegisterCertifiedAsync](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.machinelearning.executionprovidercatalog.ensureandregistercertifiedasync) to load the available models. Call **GetReadyState** on the **VideoScalar** class to determine if the video scaler is ready to process frames. If not, call **EnsureReadyAsync** to initialize the video scaler.

```csharp

private VideoScaler? _videoScaler;

protected override async Task LoadModelAsync(SampleNavigationParameters sampleParams)
{
    try
    {

        var catalog = ExecutionProviderCatalog.GetDefault();
        await catalog.EnsureAndRegisterCertifiedAsync();

        var readyState = VideoScaler.GetReadyState();
        if (readyState == AIFeatureReadyState.NotSupportedOnCurrentSystem)
        {
            // VSR cannot run on this device. Fall back to a non-VSR pipeline
            // (for example, a bilinear or bicubic upscaler) or hide the feature.
            ShowException(null, "Video Super Resolution is not supported on this device.");
            return;
        }
        if (readyState == AIFeatureReadyState.NotReady || readyState == AIFeatureReadyState.EnsureNeeded)
        {
            var operation = await VideoScaler.EnsureReadyAsync();

            if (operation.Status != AIFeatureReadyResultState.Success)
            {
                ShowException(null, "Video Scaler is not available.");
                return;
            }
        }

        _videoScaler = await VideoScaler.CreateAsync();
    }
    catch (Exception ex)
    {
        ShowException(ex, "Failed to load model.");
    }

    sampleParams.NotifyCompletion();
}
```

## Scale a VideoFrame

The following code example uses the **VideoScaler.ScaleFrame** method to upscale image data contained in a [VideoFrame](/uwp/api/windows.media.videoframe) object. You can get **VideoFrame** from a camera by using the [MediaFrameReader](/uwp/api/windows.media.capture.frames.mediaframereader) class. For more information, see [Process media frames with MediaFrameReader](/windows/apps/develop/camera/process-media-frames-with-mediaframereader). You can also use the WinUI Community Toolkit [CameraPreview](/dotnet/communitytoolkit/windows/camerapreview/) control to get **VideoFrame** objects from the camera. 

Next, a [Direct3DSurface](/uwp/api/windows.graphics.directx.direct3d11.idirect3dsurface) is obtained from the input video frame and another **Direct3DSurface** is created for the output of the upscaling. **VideoScaler.ScaleFrame** is called to upscale the frame. In this example, an **Image** control in the app's UI is updated with the upscaled frame.

```csharp
 private async Task ProcessFrame(VideoFrame videoFrame)
{
    // Process the frame with super resolution model
    var processedBitmap = await Task.Run(async () =>
    {
        int width = 0;
        int height = 0;
        var inputD3dSurface = videoFrame.Direct3DSurface;
        if (inputD3dSurface != null)
        {
            Debug.Assert(inputD3dSurface.Description.Format == Windows.Graphics.DirectX.DirectXPixelFormat.NV12, "input in NV12 format");
            width = inputD3dSurface.Description.Width;
            height = inputD3dSurface.Description.Height;
        }
        else
        {
            var softwareBitmap = videoFrame.SoftwareBitmap;
            if (softwareBitmap == null)
            {
                return null;
            }

            Debug.Assert(softwareBitmap.BitmapPixelFormat == BitmapPixelFormat.Nv12, "input in NV12 format");

            width = softwareBitmap.PixelWidth;
            height = softwareBitmap.PixelHeight;
        }

        try
        {
            if (inputD3dSurface == null)
            {
                // Create Direct3D11-backed VideoFrame for input
                using var inputVideoFrame = VideoFrame.CreateAsDirect3D11SurfaceBacked(
                    Windows.Graphics.DirectX.DirectXPixelFormat.NV12,
                    width,
                    height);

                if (inputVideoFrame.Direct3DSurface == null)
                {
                    return null;
                }

                // Copy the software bitmap to the Direct3D-backed frame
                await videoFrame.CopyToAsync(inputVideoFrame);

                inputD3dSurface = inputVideoFrame.Direct3DSurface;
            }

            // Create or resize output surface (BGRA8 format for display)
            if (_outputD3dSurface == null || _outputWidth != width || _outputHeight != height)
            {
                _outputD3dSurface?.Dispose();

                // DXGI_FORMAT_B8G8R8A8_UNORM = 87
                _outputD3dSurface = Direct3DExtensions.CreateDirect3DSurface(87, width, height);
                _outputWidth = width;
                _outputHeight = height;
            }

            // Scale the frame using VideoScaler
            var result = _videoScaler!.ScaleFrame(inputD3dSurface, _outputD3dSurface, new VideoScalerOptions());

            if (result.Status == ScaleFrameStatus.Success)
            {
                var outputBitmap = await SoftwareBitmap.CreateCopyFromSurfaceAsync(
                    _outputD3dSurface,
                    BitmapAlphaMode.Premultiplied);

                return outputBitmap;
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine($"ProcessFrame error: {ex.Message}");
        }

        return null;
    });

    if (processedBitmap == null)
    {
        return;
    }

    DispatcherQueue.TryEnqueue(async () =>
    {
        using (processedBitmap)
        {
            var source = new SoftwareBitmapSource();
            await source.SetBitmapAsync(processedBitmap);
            ProcessedVideoImage.Source = source;
        }
    });
}
```

## Scale a SoftwareBitmap using ImageBuffer

The following code example demonstrates the use of **VideoScalar** class to upscale a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap). This example does not represent a typical usage of the VSR APIs. It is less performant than using Direct3D. But you can use this example to experiment with the VSR APIs without setting up a camera or video streaming pipeline. Because the video scaler requires a **BGR8** when using an **ImageBuffer**, some helper methods are required to convert the pixel format of the supplied **SoftwareBitmap**.

The example code in this article is based on the VSR component of the [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)

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

### Software bitmap extension methods

The following helper methods convert a **SoftwareBitmap** between **BGRA8** and **BGR8** formats to match the input and output requirements of the video scalar.

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

We've followed core principles and practices described in the [Microsoft Responsible AI Standards](https://www.microsoft.com/ai/principles-and-approach) to ensure these APIs are trustworthy, secure, and built responsibly. For more details on implementing AI features in your app, see [Responsible Generative AI Development on Windows](/windows/ai/rai).

These VSR APIs use Machine Learning (ML) models, were designed specifically for scenarios such as video calling and conferencing apps and social and short-form videos that feature human faces speaking. Therefore, we do not recommend using these APIs for images in the following scenarios:

- Where the images contain potentially sensitive content and inaccurate descriptions could be controversial, such as flags, maps, globes, cultural symbols, or religious symbols.
- When accurate descriptions are critical, such as for medical advice or diagnosis, legal content, or financial documents.

## See also

- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
