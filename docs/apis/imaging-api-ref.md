---
title: API ref for AI imaging features in the Windows App SDK
description: Learn about the Windows App SDK APIs, backed by artificial intelligence (AI), that can both scale and sharpen images (Image Super Resolution) as well as identify objects within an image (Image Segmentation).
ms.topic: article
ms.date: 11/05/2024
ms.author: kbridge
author: karl-bridge-microsoft
---

# API ref for AI imaging features in the Windows App SDK

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a new [Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo. (*Make sure you include **Imaging** in the title!*)

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/) APIs, backed by artificial intelligence (AI), that can both scale and sharpen images (Image Super Resolution) as well as identify objects within an image (Image Segmentation).

For more details, see [Get Started with AI imaging in the Windows App SDK](imaging.md).

> [!IMPORTANT]
> **This feature is not yet available.** It is expected to ship in an upcoming [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.
>
> The Windows App SDK [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. They are not supported for use in production environments, and apps that use experimental features cannot be published to the Microsoft Store.

---


<!---
-api-id: N:Microsoft.Graphics.Imaging
-api-type: winrt namespace
--->

## Microsoft.Graphics.Imaging namespace

Provides APIs for machine learning models that scale and sharpen images.

<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageBuffer
-api-type: winrt class
--->

### Microsoft.Graphics.Imaging.ImageBuffer class

```
public sealed class ImageBuffer : System.IDisposable
```

<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Buffer
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.Buffer property

```
public Windows.Storage.Streams.IBuffer Buffer { get; }
```

##### -property-value

<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.BufferLength
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.BufferLength property

```
public uint BufferLength { get; }
```

##### -property-value

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.Close
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.Close method

```
// This member is not implemented in C#
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CopyToBuffer(System.Byte[])
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CopyToBuffer(System.Byte[]) method

```
public void CopyToBuffer (byte[] values);
```

##### -parameters

###### -param values


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateBufferAttachedToBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

##### -parameters

###### -param softwareBitmap

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateCopyFromBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

##### -parameters

###### -param softwareBitmap

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBuffer(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBuffer(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateFromBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height);
```

##### -parameters

###### -param buffer

###### -param pixelFormat

###### -param width

###### -param height

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBufferWithStride(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32,System.UInt32)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBufferWithStride(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32,System.UInt32) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateFromBufferWithStride (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height, uint stride);
```

##### -parameters

###### -param buffer

###### -param pixelFormat

###### -param width

###### -param height

###### -param stride

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateSoftwareBitmap
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.CreateSoftwareBitmap method

```
public Windows.Graphics.Imaging.SoftwareBitmap CreateSoftwareBitmap ();
```

##### -returns


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Height
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.Height property

```
public uint Height { get; }
```

##### -property-value


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt constructor
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32) constructor

```
public ImageBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height);
```

##### -parameters

###### -param buffer

###### -param pixelFormat

###### -param width

###### -param height


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.PixelFormat
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.PixelFormat property

```
public Microsoft.Graphics.Imaging.PixelFormat PixelFormat { get; }
```

##### -property-value


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Width
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageBuffer.Width property

```
public uint Width { get; }
```

##### -property-value


<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectExtractor
-api-type: winrt class
--->

### Microsoft.Graphics.Imaging.ImageObjectExtractor class

```
public sealed class ImageObjectExtractor : System.IDisposable
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.Close
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.Close method

```
// This member is not implemented in C#
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithImageBufferAsync(Microsoft.Graphics.Imaging.ImageBuffer)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithImageBufferAsync(Microsoft.Graphics.Imaging.ImageBuffer) method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectExtractor> CreateWithImageBufferAsync (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer);
```

##### -parameters

###### -param imageBuffer

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithSoftwareBitmapAsync(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithSoftwareBitmapAsync(Windows.Graphics.Imaging.SoftwareBitmap) method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectExtractor> CreateWithSoftwareBitmapAsync (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

##### -parameters

###### -param softwareBitmap

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.GetImageBufferObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.GetImageBufferObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint) method

```
public Microsoft.Graphics.Imaging.ImageBuffer GetImageBufferObjectMask (Microsoft.Graphics.Imaging.ImageObjectExtractorHint hint);
```

##### -parameters

###### -param hint

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.GetSoftwareBitmapObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.GetSoftwareBitmapObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint) method

```
public Windows.Graphics.Imaging.SoftwareBitmap GetSoftwareBitmapObjectMask (Microsoft.Graphics.Imaging.ImageObjectExtractorHint hint);
```

##### -parameters

###### -param hint

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.IsAvailable
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.IsAvailable method

```
public static bool IsAvailable ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.MakeAvailableAsync
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractor.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```

##### -returns


<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectExtractorHint
-api-type: winrt class
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractorHint class

```
public sealed class ImageObjectExtractorHint
```

<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.ExcludePoints
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractorHint.ExcludePoints property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.PointInt32> ExcludePoints { get; }
```

##### -property-value


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.#ctor(Windows.Foundation.Collections.IVector{Windows.Graphics.RectInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32})
-api-type: winrt constructor
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractorHint.#ctor(Windows.Foundation.Collections.IVector{Windows.Graphics.RectInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32}) constructor

```
public ImageObjectExtractorHint (System.Collections.Generic.IList<Windows.Graphics.RectInt32> includeRects, System.Collections.Generic.IList<Windows.Graphics.PointInt32> includePoints, System.Collections.Generic.IList<Windows.Graphics.PointInt32> excludePoints);
```

##### -parameters

###### -param includeRects

###### -param includePoints

###### -param excludePoints


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludePoints
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludePoints property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.PointInt32> IncludePoints { get; }
```

##### -property-value


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludeRects
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludeRects property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.RectInt32> IncludeRects { get; }
```

##### -property-value



<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectRemover
-api-type: winrt class
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover class

```
public sealed class ImageObjectRemover : System.IDisposable
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.Close
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.Close method

```
// This member is not implemented in C#
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.CreateAsync
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectRemover> CreateAsync ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.IsAvailable
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.IsAvailable method

```
public static bool IsAvailable ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.MakeAvailableAsync
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Graphics.Imaging.ImageBuffer)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Graphics.Imaging.ImageBuffer) method

```
public Microsoft.Graphics.Imaging.ImageBuffer RemoveFromImageBuffer (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, Microsoft.Graphics.Imaging.ImageBuffer imageBufferMask);
```

##### -parameters

###### -param imageBuffer

###### -param imageBufferMask

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,Windows.Graphics.Imaging.SoftwareBitmap) method

```
public Windows.Graphics.Imaging.SoftwareBitmap RemoveFromSoftwareBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap, Windows.Graphics.Imaging.SoftwareBitmap softwareBitmapMask);
```

##### -parameters

###### -param softwareBitmap

###### -param softwareBitmapMask

##### -returns



<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageScaler
-api-type: winrt class
--->

#### Microsoft.Graphics.Imaging.ImageScaler class

```
public sealed class ImageScaler : System.IDisposable
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.Close
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.Close method

```
// This member is not implemented in C#
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.CreateAsync
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageScaler> CreateAsync ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.IsAvailable
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.IsAvailable method

```
public static bool IsAvailable ();
```

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.MakeAvailableAsync
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```

##### -returns


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageScaler.MaxSupportedScaleFactor
-api-type: winrt property
--->

#### Microsoft.Graphics.Imaging.ImageScaler.MaxSupportedScaleFactor property

```
public int MaxSupportedScaleFactor { get; }
```

##### -property-value


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.ScaleImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,System.Int32,System.Int32)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.ScaleImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,System.Int32,System.Int32) method

```
public Microsoft.Graphics.Imaging.ImageBuffer ScaleImageBuffer (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, int width, int height);
```

##### -parameters

###### -param imageBuffer

###### -param width

###### -param height

##### -returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.ScaleSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,System.Int32,System.Int32)
-api-type: winrt method
--->

#### Microsoft.Graphics.Imaging.ImageScaler.ScaleSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,System.Int32,System.Int32) method

```
public Windows.Graphics.Imaging.SoftwareBitmap ScaleSoftwareBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap, int width, int height);
```

##### -parameters

###### -param softwareBitmap

###### -param width

###### -param height

##### -returns


<!---
-api-id: T:Microsoft.Graphics.Imaging.PixelFormat
-api-type: winrt enum
--->

#### Microsoft.Graphics.Imaging.PixelFormat enum

```
public enum PixelFormat
```

##### -enum-fields

###### -field Undefined: 0

###### -field Rgb24: 1

###### -field Argb32: 2

###### -field Rgba32: 3

###### -field Bgra32: 4

###### -field Gray8: 5


<!---
-api-id: T:Microsoft.Graphics.Imaging.SegmentationPoint
-api-type: winrt struct
--->

#### Microsoft.Graphics.Imaging.SegmentationPoint struct

```
public struct SegmentationPoint
```

##### -struct-fields

###### -field type

###### -field x

###### -field y


<!---
-api-id: T:Microsoft.Graphics.Imaging.SegmentationPointType
-api-type: winrt enum
--->

#### Microsoft.Graphics.Imaging.SegmentationPointType enum

```
public enum SegmentationPointType
```

##### -enum-fields

###### -field Exclude: 0

###### -field Include: 1

###### -field UpperLeft: 2

###### -field LowerRight: 3





## Related content

- [Get Started with AI imaging in the Windows App SDK](imaging.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
