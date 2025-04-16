---
title: API ref for AI imaging features in the Windows App SDK
description: Learn about the Windows App SDK APIs, backed by artificial intelligence (AI), that can both scale and sharpen images (Image Super Resolution) as well as identify objects within an image (Image Segmentation).
ms.topic: article
ms.date: 04/14/2025
---

# API ref for AI imaging in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.
>
> - Image Description features are not available in China

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/) imaging APIs, backed by artificial intelligence (AI), that support the following capabilities:

- [**Image Super Resolution**](imaging.md#what-can-i-do-with-image-super-resolution): scaling and sharpening an image.
- [**Image Description**](imaging.md#what-can-i-do-with-image-description): generating text that describes an image.
- [**Image Segmentation**](imaging.md#what-can-i-do-with-image-segmentation): identifying objects within an image.
- [**Object Erase**](imaging.md#what-can-i-do-with-object-erase): removing objects from an image.

For more details, see [Get Started with AI imaging in the Windows App SDK](imaging.md).

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Imaging** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

---


<!---
-api-id: N:Microsoft.Graphics.Imaging
-api-type: winrt namespace
--->

## Microsoft<wbr>.Graphics<wbr>.Imaging namespace

Provides APIs for machine learning models that scale and sharpen images.

<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageBuffer
-api-type: winrt class
--->

### ImageBuffer class

```
public sealed class ImageBuffer : System.IDisposable
```

Represents an uncompressed bitmap for efficient cross-process marshaling.

#### Remarks

ImageBuffer can be used with AI model APIs such as TextRecognizer that require image data. Typical usage involves creating an ImageBuffer from an existing SoftwareBitmap.


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Buffer
-api-type: winrt property
--->

#### ImageBuffer.Buffer property

```
public Windows.Storage.Streams.IBuffer Buffer { get; }
```

Gets the current image buffer.

##### Property value

The current image buffer.

<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.BufferLength
-api-type: winrt property
--->

#### ImageBuffer<wbr>.BufferLength property

```
public uint BufferLength { get; }
```

Gets the length of the image buffer.

##### Property value

The length of the image buffer.

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.Close
-api-type: winrt method
--->

#### ImageBuffer<wbr>.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.


##### Remarks

Not implemented in C#.

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CopyToBuffer(System.Byte[])
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CopyToBuffer(System<wbr>.Byte[]) method

```
public void CopyToBuffer (byte[] values);
```

Copies the current buffer into the provided target buffer.

##### Parameters

###### values

Vector of bytes in the buffer.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CreateBufferAttachedToBitmap(Windows<wbr>.Graphics<wbr>.Imaging<wbr>.SoftwareBitmap) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateBufferAttachedToBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

Create a new ImageBuffer from an existing SotftwareBitmap by getting an IMemoryBufferReference from the bitmap object.

##### Parameters

###### softwareBitmap

The SotftwareBitmap to create the ImageBuffer from.

##### Returns

The ImageBuffer or null if it's an unsupported format.

##### Remarks

The SoftwareBitmap is locked until the async operation completes and the new ImageBuffer is destroyed.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CreateCopyFromBitmap(Windows<wbr>.Graphics<wbr>.Imaging<wbr>.SoftwareBitmap) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateCopyFromBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

Create a new ImageBuffer from an existing SotftwareBitmap by copying out the underlying bitmap data.

##### Parameters

###### softwareBitmap

The SotftwareBitmap to create the ImageBuffer from.

##### Returns

The ImageBuffer or null if it's an unsupported format.

##### Remarks

The SoftwareBitmap is locked until the async operation completes and the new ImageBuffer is destroyed.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBuffer(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CreateFromBuffer(Windows<wbr>.Storage<wbr>.Streams<wbr>.IBuffer,Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.PixelFormat,System<wbr>.UInt32,System<wbr>.UInt32) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateFromBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height);
```

##### Parameters

###### buffer

###### pixelFormat

###### width

###### height

##### Returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateFromBufferWithStride(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32,System.UInt32)
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CreateFromBufferWithStride(Windows<wbr>.Storage<wbr>.Streams<wbr>.IBuffer,Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.PixelFormat,System<wbr>.UInt32,System<wbr>.UInt32,System<wbr>.UInt32) method

```
public static Microsoft.Graphics.Imaging.ImageBuffer CreateFromBufferWithStride (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height, uint stride);
```

##### Parameters

###### buffer

###### pixelFormat

###### width

###### height

###### stride

##### Returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.CreateSoftwareBitmap
-api-type: winrt method
--->

#### ImageBuffer<wbr>.CreateSoftwareBitmap method

```
public Windows.Graphics.Imaging.SoftwareBitmap CreateSoftwareBitmap ();
```

Create a new SoftwareBitmap of pixel type BGRA32 from the pixel data stored in an ImageBuffer.

##### Returns

The new SoftwareBitmap of pixel type BGRA32.


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Height
-api-type: winrt property
--->

#### ImageBuffer<wbr>.Height property

```
public uint Height { get; }
```

Gets the height of the image, in pixels.

##### Property value

The height of the image, in pixels.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Graphics.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt constructor
--->

#### ImageBuffer<wbr>.#ctor(Windows<wbr>.Storage<wbr>.Streams<wbr>.IBuffer,Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.PixelFormat,System<wbr>.UInt32,System<wbr>.UInt32) constructor

```
public ImageBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Graphics.Imaging.PixelFormat pixelFormat, uint width, uint height);
```

Initializes a new instance of the ImageBuffer class.

##### Parameters

###### buffer

The ImageBuffer.

###### pixelFormat

The pixel format of the image.

###### width

The width of the image, in pixels.

###### height

The height of the image, in pixels.


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.PixelFormat
-api-type: winrt property
--->

#### ImageBuffer<wbr>.PixelFormat property

```
public Microsoft.Graphics.Imaging.PixelFormat PixelFormat { get; }
```

Gets the pixel format of the image.

##### Property value

The pixel format of the image.


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageBuffer.Width
-api-type: winrt property
--->

#### ImageBuffer<wbr>.Width property

```
public uint Width { get; }
```

Gets the width of the image, in pixels.

##### Property value

The width of the image, in pixels.


<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectExtractor
-api-type: winrt class
--->

### ImageObjectExtractor class

```
public sealed class ImageObjectExtractor : System.IDisposable
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.Close
-api-type: winrt method
--->

#### ImageObjectExtractor.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.


##### Remarks

Not implemented in C#.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithImageBufferAsync(Microsoft.Graphics.Imaging.ImageBuffer)
-api-type: winrt method
--->

#### ImageObjectExtractor<wbr>.CreateWithImageBufferAsync(Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.ImageBuffer) method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectExtractor> CreateWithImageBufferAsync (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer);
```

##### Parameters

###### imageBuffer

##### Returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.CreateWithSoftwareBitmapAsync(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### ImageObjectExtractor<wbr>.CreateWithSoftwareBitmapAsync(Windows<wbr>.Graphics<wbr>.Imaging<wbr>.SoftwareBitmap) method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectExtractor> CreateWithSoftwareBitmapAsync (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
```

##### Parameters

###### softwareBitmap

##### Returns




<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.EnsureReadyAsync
-api-type: winrt method
--->

#### ImageObjectExtractor.EnsureReadyAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.AIFeatureReadyResult,double> EnsureReadyAsync ();
```

##### Returns




<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.GetImageBufferObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint)
-api-type: winrt method
--->

#### ImageObjectExtractor<wbr>.GetImageBufferObjectMask(Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.ImageObjectExtractorHint) method

```
public Microsoft.Graphics.Imaging.ImageBuffer GetImageBufferObjectMask (Microsoft.Graphics.Imaging.ImageObjectExtractorHint hint);
```

##### Parameters

###### hint

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.GetReadyState
-api-type: winrt method
--->

#### ImageObjectExtractor.GetReadyState method

```
public static Microsoft.Windows.AI.AIFeatureReadyState GetReadyState ();
```

##### Returns




<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractor.GetSoftwareBitmapObjectMask(Microsoft.Graphics.Imaging.ImageObjectExtractorHint)
-api-type: winrt method
--->

#### ImageObjectExtractor<wbr>.GetSoftwareBitmapObjectMask(Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.ImageObjectExtractorHint) method

```
public Windows.Graphics.Imaging.SoftwareBitmap GetSoftwareBitmapObjectMask (Microsoft.Graphics.Imaging.ImageObjectExtractorHint hint);
```

##### Parameters

###### hint

##### Returns



<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectExtractorHint
-api-type: winrt class
--->

### ImageObjectExtractorHint class

```
public sealed class ImageObjectExtractorHint
```

<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.ExcludePoints
-api-type: winrt property
--->

#### ImageObjectExtractorHint<wbr>.ExcludePoints property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.PointInt32> ExcludePoints { get; }
```

##### Property value


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.#ctor(Windows.Foundation.Collections.IVector{Windows.Graphics.RectInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32},Windows.Foundation.Collections.IVector{Windows.Graphics.PointInt32})
-api-type: winrt constructor
--->

#### ImageObjectExtractorHint<wbr>.#ctor(Windows<wbr>.Foundation<wbr>.Collections<wbr>.IVector{Windows<wbr>.Graphics<wbr>.RectInt32},Windows<wbr>.Foundation<wbr>.Collections<wbr>.IVector{Windows<wbr>.Graphics<wbr>.PointInt32},Windows<wbr>.Foundation<wbr>.Collections<wbr>.IVector{Windows<wbr>.Graphics<wbr>.PointInt32}) constructor

```
public ImageObjectExtractorHint (System.Collections.Generic.IList<Windows.Graphics.RectInt32> includeRects, System.Collections.Generic.IList<Windows.Graphics.PointInt32> includePoints, System.Collections.Generic.IList<Windows.Graphics.PointInt32> excludePoints);
```

##### Parameters

###### includeRects

###### includePoints

###### excludePoints


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludePoints
-api-type: winrt property
--->

#### ImageObjectExtractorHint<wbr>.IncludePoints property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.PointInt32> IncludePoints { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageObjectExtractorHint.IncludeRects
-api-type: winrt property
--->

#### ImageObjectExtractorHint<wbr>.IncludeRects property

```
public System.Collections.Generic.IReadOnlyList<Windows.Graphics.RectInt32> IncludeRects { get; }
```

##### Property value





<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageObjectRemover
-api-type: winrt class
--->

### Microsoft.Graphics.Imaging.ImageObjectRemover class

```
public sealed class ImageObjectRemover : System.IDisposable
```



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.Close
-api-type: winrt method
--->

#### ImageObjectRemover.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.


##### Remarks

Not implemented in C#.



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.CreateAsync
-api-type: winrt method
--->

#### ImageObjectRemover.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageObjectRemover> CreateAsync ();
```

##### Returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.EnsureReadyAsync
-api-type: winrt method
--->

#### ImageObjectRemover.EnsureReadyAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.AIFeatureReadyResult,double> EnsureReadyAsync ();
```

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.GetReadyState
-api-type: winrt method
--->

#### ImageObjectRemover.GetReadyState method

```
public static Microsoft.Windows.AI.AIFeatureReadyState GetReadyState ();
```

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Graphics.Imaging.ImageBuffer)
-api-type: winrt method
--->

#### ImageObjectRemover.RemoveFromImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Graphics.Imaging.ImageBuffer) method

```
public Microsoft.Graphics.Imaging.ImageBuffer RemoveFromImageBuffer (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, Microsoft.Graphics.Imaging.ImageBuffer imageBufferMask);
```

##### Parameters

###### imageBuffer

###### imageBufferMask

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageObjectRemover.RemoveFromSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### ImageObjectRemover.RemoveFromSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,Windows.Graphics.Imaging.SoftwareBitmap) method

```
public Windows.Graphics.Imaging.SoftwareBitmap RemoveFromSoftwareBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap, Windows.Graphics.Imaging.SoftwareBitmap softwareBitmapMask);
```

##### Parameters

###### softwareBitmap

###### softwareBitmapMask

##### Returns





<!---
-api-id: T:Microsoft.Graphics.Imaging.ImageScaler
-api-type: winrt class
--->

### ImageScaler class

```
public sealed class ImageScaler : System.IDisposable
```

<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.Close
-api-type: winrt method
--->

#### ImageScaler<wbr>.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.


##### Remarks

Not implemented in C#.


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.CreateAsync
-api-type: winrt method
--->

#### ImageScaler<wbr>.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Graphics.Imaging.ImageScaler> CreateAsync ();
```

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.EnsureReadyAsync
-api-type: winrt method
--->

#### ImageScaler.EnsureReadyAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.AIFeatureReadyResult,double> EnsureReadyAsync ();
```

##### Returns



<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.GetReadyState
-api-type: winrt method
--->

#### ImageScaler.GetReadyState method

```
public static Microsoft.Windows.AI.AIFeatureReadyState GetReadyState ();
```

##### Returns



<!---
-api-id: P:Microsoft.Graphics.Imaging.ImageScaler.MaxSupportedScaleFactor
-api-type: winrt property
--->

#### ImageScaler<wbr>.MaxSupportedScaleFactor property

```
public int MaxSupportedScaleFactor { get; }
```

##### Property value


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.ScaleImageBuffer(Microsoft.Graphics.Imaging.ImageBuffer,System.Int32,System.Int32)
-api-type: winrt method
--->

#### ImageScaler<wbr>.ScaleImageBuffer(Microsoft<wbr>.Graphics<wbr>.Imaging<wbr>.ImageBuffer,System<wbr>.Int32,System<wbr>.Int32) method

```
public Microsoft.Graphics.Imaging.ImageBuffer ScaleImageBuffer (Microsoft.Graphics.Imaging.ImageBuffer imageBuffer, int width, int height);
```

##### Parameters

###### imageBuffer

###### width

###### height

##### Returns


<!---
-api-id: M:Microsoft.Graphics.Imaging.ImageScaler.ScaleSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap,System.Int32,System.Int32)
-api-type: winrt method
--->

#### ImageScaler<wbr>.ScaleSoftwareBitmap(Windows<wbr>.Graphics<wbr>.Imaging<wbr>.SoftwareBitmap,System<wbr>.Int32,System<wbr>.Int32) method

```
public Windows.Graphics.Imaging.SoftwareBitmap ScaleSoftwareBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap, int width, int height);
```

##### Parameters

###### softwareBitmap

###### width

###### height

##### Returns


<!---
-api-id: T:Microsoft.Graphics.Imaging.PixelFormat
-api-type: winrt enum
--->

#### PixelFormat enum

```
public enum PixelFormat
```

Specifies the types of binary layouts for the underlying bitmap data.

##### Enum fields

###### Undefined: 0

Binary format is undefined.

###### Rgb24: 1

The binary format is 24 bits per pixel; 8 bits each are used for the red, green, and blue components.

###### Argb32: 2

The binary format 32 bits per pixel; 8 bits each are used for the alpha, red, green, and blue components.

###### Rgba32: 3

The binary format is 32 bits per pixel; 8 bits each are used for the red, green, blue, and alpha components. The color components are stored in red, green, blue, and alpha order.

###### Bgra32: 4

The binary format is 32 bits per pixel; 8 bits each are used for the blue, green, red, and alpha components. The color components are stored in blue, green, red, and alpha order.

###### Gray8: 5

The binary format is 16 bits per pixel. The color information specifies 65536 shades of gray.


## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Get Started with AI imaging in the Windows App SDK](imaging.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
