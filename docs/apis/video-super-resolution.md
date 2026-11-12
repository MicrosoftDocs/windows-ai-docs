---
title: Video super resolution in the Windows App SDK
description: Learn how Windows App SDK video super resolution upscales video frames to reduce bandwidth while preserving high-resolution image quality.
author: GrantMeStrength
ms.author: jken
ms.topic: get-started
ms.date: 11/12/2026
dev_langs:
- csharp
- cpp
---

# Get started with video super resolution (VSR)

Video Super Resolution (VSR) uses a machine learning model to upscale low-resolution video streams of people, restoring sharpness and detail that would otherwise be lost due to bandwidth limitations, poor network conditions, compression, or lower-quality source content.

> [!div class="button"]
> [Try video super resolution in AI Dev Gallery](aidevgallery://apis/1698eb3d-6c4f-4de0-aab8-72edca349eec?src=docs)

AI Dev Gallery provides an interactive sample that lets you compare the enhanced video, inspect the source, and export the sample as a Visual Studio project.

Adding VSR capabilities to your app enables scenarios including the following:

- Improving video quality over poor network connections
- Bandwidth optimization to reduce CDN costs
- High-bandwidth scenarios like group video calls with multiple participants
- Improving social media video quality in editing, upload or viewing

The VSR feature is available on **Copilot+ PCs** with an NPU and on devices that meet the **[recommended CPU specifications](#recommended-cpu-specifications)**. For more information, see [Develop AI applications for Copilot+ PCs](../npu-devices/index.md) and the [supported hardware table](index.md#supported-hardware).

## Prerequisites and API availability

Use a supported Windows 11 device and follow the [Windows AI APIs setup guidance](get-started.md) for OS, runtime, and deployment prerequisites. Your app uses the SDK's deployment model for the required runtime and model assets; `VideoScaler` doesn't accept application-supplied model filenames.

> [!IMPORTANT]
> The creation-options overload, explicit scenario/device/quality options, and input color properties belong to the **experimental contract version 2** surface. They aren't present in every older or stable Windows App SDK release. Check the API reference and release notes for your selected SDK package to confirm which members it exposes and the minimum Windows version and build it requires. An API's presence in development sources doesn't establish its availability in a published SDK.

## Models and device support

The following table describes each model and its device support. FaceFocused / Talking Head is the default `FaceFocused` scenario; Gaming SR corresponds to `Gaming`. Automatic device order lists the initialization fallback order among eligible devices.

| Model | Description | Automatic device order | Other device requests |
|---|---|---|---|
| FaceFocused / Talking Head | Enhances facial details captured during video calls, improving clarity while remaining efficient for real-time use. | NPU, GPU, CPU | Explicit NPU/GPU/CPU supported when eligible |
| TextFocused | Improves the clarity of camera-captured text and fine edges, especially on larger screens where compression and scaling artifacts are more noticeable. | GPU, CPU | NPU unsupported |
| Gaming SR | Enhances lower-resolution game streams on the viewer's device, improving perceived detail while allowing lower streaming bandwidth. | GPU only | CPU/NPU unsupported |
| Stabilization | Reduces unwanted camera movement for steadier real-time video, making remote collaboration and demonstrations easier to follow when the camera is handheld or moving. | CPU only | GPU/NPU unsupported |

## Supported hardware

| Hardware | Status | Details |
|---|---|---|
| NPU (Copilot+ PC) | ✅ Available | See [Copilot+ PCs developer guide](../npu-devices/index.md). |
| CPU | ✅ Available | Best on devices meeting the [recommended CPU specifications](#recommended-cpu-specifications). VSR will still attempt to run on lower-spec devices, but real-time quality and frame rate may suffer. |
| GPU | Experimental device option (contract 2) | You can request `Gpu` by using `VideoScalerCreationOptions.ExecutionDevice` in an SDK that exposes these options. Availability depends on the scenario and supported hardware. See [Choose the execution device, scenario, and quality](#choose-the-execution-device-scenario-and-quality). |

> [!NOTE]
> **Hardware selection is automatic by default.** The parameterless `VideoScaler.CreateAsync()` overload uses `FaceFocused` with automatic device and quality selection. `Automatic` permits initialization fallback; it doesn't guarantee NPU execution or bypass missing prerequisites. An explicit device request uses that device or fails. See [Supported hardware](index.md#supported-hardware) for the cross-API view.

The following resolution and frame-rate guidance applies to face-focused VSR. Frame formats depend on the processing method.

| Attribute | Supported Content |
|-------------|-------|
| Input resolution| 240p – 1440p |
| Output resolution | 480p – 1440p  (4K for offline video processing) |
| Frames-per-second (FPS) range | 15 fps – 60 fps |
| Input pixel format | RGB/BGR (`ScaleImageBuffer`), NV12 (`Scale`) |
| Output pixel format | RGB/BGR (`ScaleImageBuffer`), NV12 or supported BGRA/BGRX (`Scale`) |

## Recommended CPU specifications

Use the SDK's deployment model for runtime and model assets. On CPU devices, actual performance depends on the device, scenario, and available models.

For a good CPU experience, target devices that meet **all** of the following recommended specifications:

- **4 or more physical cores**
- **3 GHz or higher base clock**
- **32 MB or more of L3 cache**

These are recommendations, not hard minimums — the API will still attempt to scale on lower-spec devices. Apps that target a broad CPU range should query the CPU at runtime and, if the device falls below the recommended bar, fall back to a non-VSR pipeline or surface a quality-tradeoff hint to the user.

> [!NOTE]
> `GetReadyState()` is an optional capability check, not model or device preparation. It doesn't load models, initialize the requested device, or guarantee that creation or frame processing will succeed. The CPU check is a performance recommendation, not an API eligibility test; use it to guide UX choices and handle creation and processing failures separately.

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

Create a scaler once for a stream, before its frame loop, and reuse it while processing that stream. The following example uses the parameterless overload for face-focused processing with automatic device and quality selection.

`GetReadyState()` and `EnsureReadyAsync()` remain supported as optional capability checks, but neither is a prerequisite for creation. They don't download models or prepare a requested device. If you use `EnsureReadyAsync()`, handle its result and exceptions.

Handle creation failures even after a readiness check succeeds. Access restrictions, unavailable hardware, or runtime/provider setup failures can prevent creation.

```csharp
using Microsoft.Windows.AI.Video;

private VideoScaler? _videoScaler;

protected override async Task LoadModelAsync(SampleNavigationParameters sampleParams)
{
    try
    {
        _videoScaler = await VideoScaler.CreateAsync();
    }
    catch (Exception ex)
    {
        ShowException(ex, "Failed to create the video scaler.");
    }
    finally
    {
        sampleParams.NotifyCompletion();
    }
}
```

## Choose the execution device, scenario, and quality

The following opt-in controls require an SDK that exposes the experimental contract version 2 surface. The parameterless creation overload and existing frame-option defaults are unchanged; this doesn't make the new members available in older or stable SDK releases. See [Prerequisites and API availability](#prerequisites-and-api-availability).

### Creation options

Call `VideoScaler.CreateAsync(VideoScalerCreationOptions)` to configure the session when you create it. All properties are optional, and null creation options use the defaults.

| Property | Values | Default | Description |
|---|---|---|---|
| `ExecutionDevice` | `Automatic`, `Npu`, `Gpu`, `Cpu` | `Automatic` | Selects the execution device. Automatic selection permits initialization fallback; an explicit request uses that device or fails. |
| `Scenario` | `FaceFocused`, `TextFocused`, `Gaming`, `Stabilization` | `FaceFocused` | Selects one processing scenario. See [Models and device support](#models-and-device-support). |
| `QualityPreference` | `Automatic`, `FavorPerformance`, `FavorQuality` | `Automatic` | Expresses whether to favor speed or visual quality. |

> [!NOTE]
> A quality preference doesn't guarantee a particular model, output, or frame rate. Supported hardware and model choices depend on the scenario and SDK release; an enum value alone doesn't establish device eligibility.

Each instance processes one scenario; scenarios aren't combined into an implicit chain of effects. Changing the creation-options object afterward doesn't reconfigure the instance. Create a new instance for a different scenario, device, or quality request.

Stabilization is CPU-only and requires full-frame processing with dimensions of at least 32 pixels per side. Use one ordered stream per instance because stabilization retains frame history, and create a new instance for an independent stream. It can resize to the requested output dimensions; size changes reset its history.

### Per-frame color options

`VideoScalerOptions` adds properties that describe the color encoding of NV12 input frames.

| Property | Values | Default | Description |
|---|---|---|---|
| `InputColorRange` | `Full`, `Limited` | `Full` | The quantization range of the NV12 input. |
| `InputColorMatrix` | `Bt601`, `Bt709` | `Bt601` | The YUV-to-RGB conversion matrix of the NV12 input. |

Null frame options mean full-frame processing with Full-range BT.601 defaults. Set the color properties to match the actual NV12 source; RGB/BGR `ImageBuffer` data is full-range.

NV12 output inherits the declared input range and matrix, while RGB-family output is full-range. These options don't independently select output color, HDR transfer functions, or gamut conversion. A required conversion failure doesn't produce successful output with different color semantics.

### Example

The following example creates a face-focused scaler with automatic device and quality selection, and then processes one limited-range BT.709 frame. Create the scaler before your frame loop, reuse the frame options while the input encoding stays the same, and check every frame's result.

```csharp
using Microsoft.Windows.AI.Video;

try
{
    using var scaler = await VideoScaler.CreateAsync(new VideoScalerCreationOptions
    {
        Scenario = VideoScalerScenario.FaceFocused,
        ExecutionDevice = VideoScalerExecutionDevice.Automatic,
        QualityPreference = VideoScalerQualityPreference.Automatic
    });

    var frameOptions = new VideoScalerOptions
    {
        InputColorRange = VideoColorRange.Limited,
        InputColorMatrix = VideoColorMatrix.Bt709
    };

    var result = scaler.Scale(inputD3dSurface, outputD3dSurface, frameOptions);
    if (result.Status != VideoScalerStatus.Success)
    {
        System.Diagnostics.Debug.WriteLine($"Frame failed: {result.Status}");
        return;
    }
}
catch (Exception ex)
{
    System.Diagnostics.Debug.WriteLine($"VideoScaler failed: {ex.Message}");
}
```

## Process frames and handle failures

`Scale` and `ScaleImageBuffer` process frames synchronously; asynchronous creation doesn't make frame processing asynchronous. Prefer `Scale` when frames already reside in D3D11 textures, without converting them to an intermediate `ImageBuffer`. This doesn't guarantee zero-copy processing or the same transfer cost on every device.

Request the final output resolution your app needs; you don't need an intermediate 2x image. Use valid dimensions, formats, row strides, and buffer capacities, and keep both input and output resources alive until the call returns. Padded rows are supported when described correctly.

For surface processing, use different input and output texture resources. NV12 dimensions must be even, and output surfaces must be writable. Mip chains, texture arrays, and multisampling aren't supported; BGRA output is opaque, and the unused BGRX byte is also set to 255.

`VideoScalerOptions.RegionsOfInterest` contains input-coordinate rectangles with positive sizes that fit entirely inside the input frame. An empty collection means full-frame processing; invalid regions are rejected rather than clipped. Stabilization supports only full-frame processing.

Each frame call returns a `VideoScalerResult`. Check its `Status` before using the output.

| Status | Meaning |
|---|---|
| `Success` | Processing completed successfully. |
| `InvalidInputFormat` | The input format or surface isn't accepted. |
| `InvalidOutputFormat` | The output format or surface isn't accepted. |
| `InvalidPlaneSize` | Dimensions, layout, stride, or capacity are invalid. |
| `InvalidFrameData` | Frame data is missing or invalid, including invalid regions of interest. |
| `ModelNotAvailable` | The processing implementation is unavailable. |
| `OutOfMemory` | Memory is insufficient. |
| `Failure` | Another processing failure occurred. |

Frame calls can also raise API, argument, or lifetime exceptions, so handle exceptions as well as returned statuses. Don't use failed-frame output as valid data; decide whether to retry or drop the frame. An earlier success doesn't guarantee a later success, and a frame failure doesn't automatically switch the instance to another device.

Dispose or close the scaler when the stream finishes, after processing calls have returned. Calls after `Close()` or the projected `Dispose()` are rejected.

## Scale a VideoFrame

The following code example uses the **VideoScaler.Scale** method to upscale image data contained in a [VideoFrame](/uwp/api/windows.media.videoframe) object. You can get **VideoFrame** from a camera by using the [MediaFrameReader](/uwp/api/windows.media.capture.frames.mediaframereader) class. For more information, see [Process media frames with MediaFrameReader](/windows/apps/develop/camera/process-media-frames-with-mediaframereader). You can also use the WinUI Community Toolkit [CameraPreview](/dotnet/communitytoolkit/windows/camerapreview/) control to get **VideoFrame** objects from the camera. 

Next, a [Direct3DSurface](/uwp/api/windows.graphics.directx.direct3d11.idirect3dsurface) is obtained from the input video frame and another **Direct3DSurface** is created for the output of the upscaling. This example doubles the input width and height to demonstrate 2x upscaling; choose input dimensions that keep the output within the supported resolution limits. **VideoScaler.Scale** is called to upscale the frame. In this example, an **Image** control in the app's UI is updated with the upscaled frame.

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
            int outputWidth = checked(width * 2);
            int outputHeight = checked(height * 2);

            using var inputVideoFrame = inputD3dSurface == null
                ? VideoFrame.CreateAsDirect3D11SurfaceBacked(
                    Windows.Graphics.DirectX.DirectXPixelFormat.NV12,
                    width,
                    height)
                : null;

            if (inputVideoFrame != null)
            {
                if (inputVideoFrame.Direct3DSurface == null)
                {
                    return null;
                }

                // Copy the software bitmap to the Direct3D-backed frame
                await videoFrame.CopyToAsync(inputVideoFrame);

                inputD3dSurface = inputVideoFrame.Direct3DSurface;
            }

            // Create or resize output surface (BGRA8 format for display)
            if (_outputD3dSurface == null || _outputWidth != outputWidth || _outputHeight != outputHeight)
            {
                _outputD3dSurface?.Dispose();

                // DXGI_FORMAT_B8G8R8A8_UNORM = 87
                _outputD3dSurface = Direct3DExtensions.CreateDirect3DSurface(87, outputWidth, outputHeight);
                _outputWidth = outputWidth;
                _outputHeight = outputHeight;
            }

            // Scale the frame using VideoScaler
            var result = _videoScaler!.Scale(inputD3dSurface, _outputD3dSurface, new VideoScalerOptions());

            if (result.Status == VideoScalerStatus.Success)
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

The following example processes a [SoftwareBitmap](/uwp/api/windows.graphics.imaging.softwarebitmap) with `ScaleImageBuffer`, doubling the input width and height. The output stride and buffer capacity use these larger dimensions. The helper methods convert the bitmap to a BGR8 `ImageBuffer` and convert the output to BGRA8 for display. If your frames already reside in D3D11 textures, prefer `Scale` rather than converting them to an intermediate buffer.

The example code in this article is based on the VSR component of the [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)

```csharp
    public SoftwareBitmap ScaleVideoFrame(SoftwareBitmap inputFrame)
    {
        ImageBuffer inputImageBuffer = SoftwareBitmapExtensions.ConvertToBgr8ImageBuffer(inputFrame);
        int outputWidth = checked(inputFrame.PixelWidth * 2);
        int outputHeight = checked(inputFrame.PixelHeight * 2);
        int outputStride = checked(outputWidth * 3);
        uint size = checked((uint)outputStride * (uint)outputHeight);
        IBuffer outputBuffer = new global::Windows.Storage.Streams.Buffer(size);
        outputBuffer.Length = size;
        ImageBuffer outputImageBuffer = ImageBuffer.CreateForBuffer(
            outputBuffer,
            ImageBufferPixelFormat.Bgr8,
            outputWidth,
            outputHeight,
            outputStride);
        var result = _videoScaler!.ScaleImageBuffer(inputImageBuffer, outputImageBuffer, new VideoScalerOptions());
        if (result.Status != VideoScalerStatus.Success)
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

We've followed core principles and practices described in the [Microsoft Responsible AI Standards](https://www.microsoft.com/ai/principles-and-approach) to ensure these APIs are trustworthy, secure, and built responsibly. For guidance on adding model-based video enhancement to your app, see [Responsible Generative AI Development on Windows](/windows/ai/rai).

The default `FaceFocused` scenario is designed for video calling, conferencing, and social and short-form videos that feature human faces speaking. We don't recommend using super resolution for videos in the following scenarios:

- Where the video contains potentially sensitive content and upscaling could introduce misleading detail or alter identity or facial features, such as footage of individuals, cultural symbols, or religious symbols.
- When faithful, unaltered video is critical, such as for medical imaging, legal or forensic evidence, or identity verification.

## See also

- [Explore and export samples with AI Dev Gallery](../ai-dev-gallery/index.md)
- [Windows AI API samples](https://github.com/microsoft/WindowsAppSDK-Samples/tree/release/experimental/Samples/WindowsAIFoundry)
