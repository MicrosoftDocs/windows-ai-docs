---
title: Video super resolution in the Windows App SDK
description: Learn how Windows App SDK video super resolution upscales video frames to reduce bandwidth while preserving high-resolution image quality.
author: GrantMeStrength
ms.author: jken
ms.topic: get-started
ms.date: 11/25/2026
dev_langs:
- csharp
- cpp
---

# Get started with video super resolution (VSR)

Video super resolution (VSR) lets your app upscale video frames with `VideoScaler`. Create a scaler once for each stream with `VideoScaler.CreateAsync()`, then reuse it for subsequent frames. The default is face-focused enhancement with automatic device and quality selection.

Use `Scale` for supported Direct3D 11 surfaces or `ScaleImageBuffer` for RGB/BGR image buffers. Provide an output resource at the resolution your app needs.

> [!div class="button"]
> [Try video super resolution in AI Dev Gallery](aidevgallery://apis/1698eb3d-6c4f-4de0-aab8-72edca349eec?src=docs)

AI Dev Gallery provides an interactive sample that lets you compare the enhanced video, inspect the source, and export the sample as a Visual Studio project.

Adding VSR capabilities to your app enables scenarios including the following:

- Improving video quality over poor network connections
- Bandwidth optimization to reduce CDN costs
- High-bandwidth scenarios like group video calls with multiple participants
- Improving social media video quality in editing, upload or viewing

The VSR feature is available on **Copilot+ PCs** with an NPU, devices with a supported GPU, and CPU devices. GPU support extends VSR to more than 400 million devices with capable GPUs. For the best CPU experience, target devices that meet the **[recommended CPU specifications](#recommended-cpu-specifications)**. For more information, see [Develop AI applications for Copilot+ PCs](../npu-devices/index.md) and the [supported hardware table](index.md#supported-hardware).

## Prerequisites and API availability

**Applies to:** Windows App SDK **2.6.6-experimental**.

Use a supported Windows 11 device and follow the [Windows AI APIs setup guidance](get-started.md) for OS, runtime, and deployment prerequisites. Your app uses the SDK's deployment model for the required runtime and model assets; `VideoScaler` doesn't accept application-supplied model filenames.

> [!IMPORTANT]
> The creation-options overload, explicit scenario/device/quality options, and input color properties described here apply to Windows App SDK **2.6.6-experimental** and belong to the **experimental contract version 2** surface. They aren't present in every older or stable Windows App SDK release. Check the API reference and release notes for your selected SDK package to confirm which members it exposes and the minimum Windows version and build it requires.

## Models and device support

The following table describes each processing scenario. FaceFocused / Talking Head is the default `FaceFocused` scenario; Gaming SR corresponds to `Gaming`. Device eligibility depends on the scenario, as described in [Device selection and fallback](#device-selection-and-fallback).

| Scenario | Description |
|---|---|
| FaceFocused / Talking Head | Enhances facial details captured during video calls, improving clarity while remaining efficient for real-time use. |
| TextFocused | Improves the clarity of camera-captured text and fine edges, especially on larger screens where compression and scaling artifacts are more noticeable. |
| Gaming SR | Enhances lower-resolution game streams on the viewer's device, improving perceived detail while allowing lower streaming bandwidth. |
| Stabilization | Reduces unwanted camera movement for steadier real-time video, making remote collaboration and demonstrations easier to follow when the camera is handheld or moving. |

## Device selection and fallback

For streaming, VSR selects the device first and the quality level second. It makes both choices once, when you create the instance. The public VSR API defaults to FaceFocused / Talking Head, `Automatic` device selection, and `Automatic` quality.

Use `Automatic` when your app can run on any device supported by the requested scenario. During creation, the scaler attempts device selection in the scenario-specific order below. It doesn't benchmark devices to select the fastest device, the lowest power consumption, or the highest quality. If you request a specific device and that device can't support the request, creation fails instead of switching devices. Quality fallback can still occur on the explicitly requested device.

Because device selection comes first, a usable WODQ NPU model takes priority over a higher-quality CPU implementation. After initialization, a frame failure, a slow frame, or a missed application deadline doesn't automatically switch the instance's device or model.

### Device order by scenario

| Scenario | When to use it | Automatic device order | Explicit device requests |
|---|---|---|---|
| `FaceFocused` (default) | Make the speaker's face clearer in a video call or recorded presentation. This is the default when you call `VideoScaler.CreateAsync()` without options. | NPU, then GPU, then CPU | NPU, GPU, or CPU, if eligible |
| `TextFocused` | Improve the clarity of camera-captured text and fine edges, such as writing on a whiteboard or print on a document. | GPU, then CPU | GPU or CPU, if eligible; NPU isn't supported |
| `Gaming` | Upscale a lower-resolution game stream on the viewer's device. Creation fails if no supported GPU is available. | GPU only | GPU only; CPU and NPU aren't supported, and there's no CPU fallback |
| `Stabilization` | Reduce camera shake rather than enhance detail. Submit full frames in order and use a separate scaler for each independent stream because stabilization retains frame history. | CPU only | CPU only; GPU and NPU aren't supported |

Fallback stays within the requested scenario: `TextFocused` doesn't switch to a `FaceFocused` model, `Gaming` doesn't fall back to CPU processing, and `Stabilization` doesn't substitute a super resolution model.

If your app explicitly requests `Gpu` and no GPU qualifies, creation fails rather than silently switching to CPU processing. A `TextFocused` app that permits CPU processing can use `Automatic`. A `Gaming` app must handle GPU unavailability because that scenario has no CPU implementation.

### Talking Head models by device and quality

For `FaceFocused`, device preference takes precedence over quality preference: the scaler considers a device, then attempts the applicable models on that device in preference order. The table shows the current choices. Model names describe implementations; they aren't API values an app can request.

| Quality request | NPU | GPU | CPU |
|---|---|---|---|
| `Automatic` / `FavorPerformance` | WODQ | Spatial SR | CPU ONNX SR |
| `FavorQuality` | M8 (higher quality), then WODQ | Spatial SR at the available quality tier | Native SR, then CPU ONNX SR |

With automatic device selection, the scaler tries these options during initialization, skipping ineligible devices and stopping when a model initializes successfully:

- **Automatic or FavorPerformance:** NPU WODQ, then GPU Spatial SR, then CPU ONNX SR.
- **FavorQuality:** NPU M8, then NPU WODQ, then GPU Spatial SR, then native CPU SR, then CPU ONNX SR.

A quality preference doesn't guarantee a particular model, output quality, or frame rate.

## Supported hardware

| Hardware | Status | Details |
|---|---|---|
| NPU (Copilot+ PC) | ✅ Available | See [Copilot+ PCs developer guide](../npu-devices/index.md). |
| CPU | ✅ Available | Supported for FaceFocused / Talking Head, TextFocused, and Stabilization, but not Gaming SR. Best on devices meeting the [recommended CPU specifications](#recommended-cpu-specifications). VSR will still attempt to run on lower-spec devices for CPU-supported scenarios, but real-time quality and frame rate may suffer. |
| GPU | ✅ Available | See [GPU requirements](#gpu-requirements). Supported GPUs can be selected automatically where eligible. Explicit `Gpu` requests require experimental contract version 2; availability depends on the scenario and hardware. See [Choose the execution device, scenario, and quality](#choose-the-execution-device-scenario-and-quality). |

> [!NOTE]
> **Hardware selection is automatic by default.** The parameterless `VideoScaler.CreateAsync()` overload uses `FaceFocused` with automatic device and quality selection. Selection happens at creation, not during frame processing. See [Device selection and fallback](#device-selection-and-fallback) for scenario-specific device and model order, and [Supported hardware](index.md#supported-hardware) for the cross-API view.

The following resolution and frame-rate guidance applies to face-focused VSR. Frame formats depend on the processing method.

| Attribute | Supported values | Performance guidance |
|---|---|---|
| Input resolution | 240p–1440p | Choose dimensions that meet your app's quality requirements and measure frame time on representative devices. See [Choose a resolution for your video stream](#choose-a-resolution-for-your-video-stream). |
| Output resolution | 480p–1440p; 4K for offline processing | Allocate the final resolution your app needs. The performance impact depends on the selected model and device. |
| Frame rate | 15–60 fps | At 30 fps, your app has about 33 ms per frame; at 60 fps, it has about 17 ms. Lowering the target frame rate gives your app more time per frame without making an individual frame faster to process and can allow higher-resolution output. |
| Input pixel format | RGB/BGR with `ScaleImageBuffer`; NV12 with `Scale` | If frames already reside in NV12 Direct3D surfaces, use `Scale` to avoid converting them to image buffers. |
| Output pixel format | RGB/BGR with `ScaleImageBuffer`; NV12 or supported BGRA/BGRX with `Scale` | |

## GPU requirements

The installed driver must expose native 16-bit floating-point (FP16) shader operations, and the GPU must have at least **512** driver-reported total lanes (`TotalLaneCount`). Meeting these requirements establishes GPU eligibility but doesn't guarantee a particular frame rate. GPUs below the minimum lane count don't qualify, even if they support native FP16 shader operations.

## Recommended CPU specifications

Use the SDK's deployment model for runtime and model assets. On CPU devices, actual performance depends on the device, scenario, and available models.

When `FaceFocused` with `FavorQuality` or `TextFocused` uses native CPU inference, the scaler limits the number of threads it can use based on the input frame's pixel count. Smaller frames have a lower limit; larger frames can use more threads. The frame-size thresholds and thread limits depend on the CPU backend.

CPU processing is supported on systems with **12 logical processors**. For better performance, we recommend **16 or more logical processors**. Neither count is a minimum requirement or a guarantee of how many threads the scaler will use.

> [!NOTE]
> `GetReadyState()` is an optional capability check, not model or device preparation. It doesn't load models, initialize the requested device, or guarantee that creation or frame processing will succeed. The CPU check is a performance recommendation, not an API eligibility test; use it to guide UX choices and handle creation and processing failures separately.

## Choose a resolution for your video stream

The performance impact of changing input or output resolution depends on the selected model and device. Reducing either resolution may improve end-to-end frame time, but doesn't necessarily reduce inference work. Choose dimensions that meet your app's quality requirements and measure performance on representative devices.

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
| `ExecutionDevice` | `Automatic`, `Npu`, `Gpu`, `Cpu` | `Automatic` | Selects the device before the quality level, once at creation. `Automatic` uses the scenario's fixed device order; an explicit request uses that device or fails. |
| `Scenario` | `FaceFocused`, `TextFocused`, `Gaming`, `Stabilization` | `FaceFocused` | Selects one processing scenario. See [Models and device support](#models-and-device-support). |
| `QualityPreference` | `Automatic`, `FavorPerformance`, `FavorQuality` | `Automatic` | Selects the quality/model order within the chosen device. Quality fallback doesn't change an explicitly requested device. |

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

NV12 output inherits the declared input range and matrix, while RGB-family output is full-range. There is no separate output color setting. These options don't independently select HDR transfer functions or gamut conversion. If a required color conversion fails, the frame operation fails.

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

`Scale` processes supported Direct3D 11 surfaces, and `ScaleImageBuffer` processes RGB/BGR image buffers. Both process frames synchronously; asynchronous creation doesn't make frame processing asynchronous. Prefer `Scale` when frames already reside in D3D11 textures, without converting them to an intermediate `ImageBuffer`. This doesn't guarantee zero-copy processing or the same transfer cost on every device.

Allocate the output surface or image buffer at the final resolution your app needs. The scaler writes the processed frame directly to that resource; you don't need an intermediate 2x image. Use valid dimensions, formats, row strides, and buffer capacities, and keep both input and output resources alive until the call returns. Padded rows are supported when described correctly.

For surface processing, use different input and output texture resources. NV12 dimensions must be even, and output surfaces must be writable. Mip chains, texture arrays, and multisampling aren't supported; BGRA output is opaque, and the unused BGRX byte is also set to 255.

`VideoScalerOptions.RegionsOfInterest` contains input-coordinate rectangles, even when the output resolution differs. Each rectangle must have a positive width and height and fit entirely inside the input frame. An empty collection means full-frame processing; invalid regions are rejected rather than clipped. Stabilization supports only full-frame processing.

Each frame call returns a `VideoScalerResult`. Use the output only when its `Status` is `VideoScalerStatus.Success`.

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

Frame calls can also raise API, argument, or lifetime exceptions, so handle exceptions as well as returned statuses. Don't use failed-frame output as valid data; decide whether to retry or drop the frame. An earlier success doesn't guarantee a later success. A frame failure, slow frame, or missed application deadline doesn't automatically switch the instance to another device or model.

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
