---
title: API ref for Text Recognition APIs in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) text recognition APIs shipping with Windows App SDK 1.6 Experimental that can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.
ms.topic: article
ms.date: 05/15/2024
ms.author: kbridge
author: karl-bridge-microsoft
---

# API ref for Text Recognition APIs in the Windows App SDK

Learn about the new Artificial Intelligence (AI) text recognition APIs shipping with Windows App SDK 1.6 Experimental that can be used to identify characters in an image, recognize words, lines, polygonal boundaries, and provide confidence levels for the generated matches.

---


<!---
-api-id: N:Microsoft.Windows.Vision
-api-type: winrt namespace
--->

## Microsoft.Windows.Vision namespace

Provides APIs for machine learning models that analyze the textual content of images.

### Remarks

### See also

### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.BoundingBox
-api-type: winrt struct
--->

### Microsoft.Windows.Vision.TextRecognition.BoundingBox struct

<!--
public struct BoundingBox
-->

A polygon with 4 points used for the boundary of recognized words and lines of text.

#### Fields

##### BottomLeft

The bottom left corner of the bounding box.

##### BottomRight

The bottom right corner of the bounding box.

##### TopLeft

The top left point of the bounding box.

##### TopRight

The top right point of the bounding box.

#### Remarks

When returned as a boundary for a word or line, the *TopLeft*, *TopRight*, *BottomRight*, and *BottomLeft* points are relative to the rotation and skew of the recognized text in the image. The following diagram shows the point layout for different text rotations where 0 is *TopLeft*, 1 is *TopRight*, 2 is *BottomRight*, and 3 is *BottomLeft*, all relative to the text.

:::image type="content" source="../images/bounding-box-examples.png" alt-text="Diagram of three bounding box examples showing how corner points are identified based on text rotation.":::

#### Examples

#### See also


<!---
-api-id: T:Microsoft.Windows.Vision.DetectedLineStyle
-api-type: winrt enum
--->

### Microsoft.Windows.Vision.DetectedLineStyle enum

<!--
public enum DetectedLineStyle
-->

Specifies the line styles that can be recognized.

#### Enum fields

##### Handwritten: 0

The line of text is hand written.

##### Other: 1

The line of text is not hand written.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.OrientationDetectionOptions
-api-type: winrt enum
--->

### Microsoft.Windows.Vision.OrientationDetectionOptions enum

<!--
public enum OrientationDetectionOptions
-->

Specifies the text orientations that can be recognized.

#### Enum fields

##### None: 0

Orientation is not recognized.

##### DetectOrientation: 1

Orientation is recognized.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedLine
-api-type: winrt class
--->

### Microsoft.Windows.Vision.RecognizedLine class

<!--
public sealed class RecognizedLine
-->

Represents a single line of recognized text.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Style
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedLine.Style property

<!--
public Microsoft.Windows.Vision.RecognizedLineStyle Style { get; }
-->

Gets the recognized line style.

##### Property value

the recognized line style.

#### Remarks

Includes whether the line of text was handwritten or not and the level of recognition confidence.

#### See-also

#### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Text
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedLine.Text property

<!--
public string Text { get; }
-->

Gets the text of the recognized line.

##### Property value

The text of the recognized line.

#### Remarks

All words concatenated with spaces.

#### See-also

#### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedLine.Words
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedLine.Words property

<!--
public Microsoft.Windows.Vision.RecognizedWord[] Words { get; }
-->

The words in the recognized line.

##### Property value

The words in the recognized line.

#### Remarks

#### See-also

#### Examples



<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedLineStyle
-api-type: winrt struct
--->

### Microsoft.Windows.Vision.RecognizedLineStyle struct

<!--
public struct RecognizedLineStyle
-->

Represents the style of the recognized line.

#### Struct fields

##### Confidence

The confidence level of the line style recognition.

##### Name

The line style name.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedText
-api-type: winrt class
--->

### Microsoft.Windows.Vision.RecognizedText class

<!--
public sealed class RecognizedText
-->

Represents the result of an image-to-text recognition operation.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedText.ImageAngle
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedText.ImageAngle property

<!--
public float ImageAngle { get; }
-->

Gets the clockwise rotational angle of the recognized text in degrees.

##### Property value

The clockwise rotational angle of the recognized text in degrees.

##### Remarks

##### See-also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedText.Lines
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedText.Lines property

<!--
public Microsoft.Windows.Vision.RecognizedLine[] Lines { get; }
-->

Gets the collection of recognized lines.

##### Property value

The collection of recognized lines.

##### Remarks

##### See-also

##### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.RecognizedWord
-api-type: winrt class
--->

### Microsoft.Windows.Vision.RecognizedWord class

<!--
public sealed class RecognizedWord
-->

Represents a single recognized word.

#### Remarks

#### See-also

#### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.BoundingBox
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedWord.BoundingBox property

<!--
public Microsoft.Windows.Vision.BoundingBox BoundingBox { get; }
-->

Gets the quadrilateral boundary of the recognized word.

##### Property value

The quadrilateral boundary of the recognized word. TopLeft is relative to the word's rotation.

##### Remarks

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Confidence
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedWord.Confidence property

<!--
public float Confidence { get; }
-->

Gets how likely this word was recognized correctly.

##### Property value

Wow likely this word was recognized correctly. Value ranges from 0.0 to 1.0, inclusive.

##### Remarks

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Text
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedWord.Text property

<!--
public string Text { get; }
-->

Gets the text of the recognized word.

##### Property value

The text of the recognized word.

##### Remarks

##### See also

##### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognizer
-api-type: winrt class
--->

### Microsoft.Windows.Vision.TextRecognition.TextRecognizer class

<!--
public sealed class TextRecognizer : System.IDisposable
-->

Recognizes words and lines, and their quadrilateral boundaries, in a source image.

### Remarks

### See also

### Examples

<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.Close
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.Close method

<!--
// This member is not implemented in C#
-->

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.

##### See also

##### Examples

<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.CreateAsync
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.CreateAsync method

<!--
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.Vision.TextRecognizer> CreateAsync ();
-->

Asynchronously creates a new instance of the TextRecognizer class.

##### Returns

A new instance of the TextRecognizer class.

This will return an error if GetModelReadyStatus is not Ready.

##### Remarks

##### See-also

##### Examples


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.IsAvailable
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.IsAvailable method

<!--
public static bool IsAvailable ();
-->

Retrieves whether the underlying language model is installed.

##### Returns

True if the underlying language model is installed. Otherwise, false.

##### Remarks

##### See-also

##### Examples


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.MakeAvailableAsync
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.MakeAvailableAsync method

<!--
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
-->

Ensures the underlying language model is installed and available for use.

##### Returns

An asynchronous action with progress that returns a [PackageDeploymentResult](/windows/windows-app-sdk/api/winrt/microsoft.windows.management.deployment.packagedeploymentresult) on completion.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImage(Microsoft.Windows.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImage(Microsoft.Windows.Imaging.ImageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions) method

<!--
public Microsoft.Windows.Vision.RecognizedText RecognizeTextFromImage (Microsoft.Windows.Imaging.ImageBuffer imageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions options);
-->

Recognize text in the provided image.

##### Parameters

###### imageBuffer

An uncompressed bitmap.

###### options

Options for configuring the text recognition model for the TextRecognizer.

##### Returns

The recognized text.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImageAsync(Microsoft.Windows.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)
-api-type: winrt method
--->

#### Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImageAsync(Microsoft.Windows.Imaging.ImageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions) method

<!--
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.Vision.RecognizedText> RecognizeTextFromImageAsync (Microsoft.Windows.Imaging.ImageBuffer imageBuffer, Microsoft.Windows.Vision.TextRecognizerOptions options);
-->

Asynchronously recognize text in the provided image.

##### Parameters

###### imageBuffer

An uncompressed bitmap.

###### options

Options for configuring the text recognition model for the TextRecognizer.

##### Returns

The recognized text.

##### Remarks

##### See also

##### Examples



<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognizerOptions
-api-type: winrt class
--->

### Microsoft.Windows.Vision.TextRecognizerOptions class

<!--
public sealed class TextRecognizerOptions
-->

Provides options to configure the text recognition model for a TextRecognizer.

#### Remarks

#### See also

#### Examples



<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.MaxAnalysisSize
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.TextRecognizerOptions.MaxAnalysisSize property

<!--
public Windows.Graphics.SizeInt32 MaxAnalysisSize { get; set; }
-->

Gets or sets the maximum image size.

##### Property value

The maximum image size. Default value is 1152 width and 768 height.

##### Remarks

This size is a suggestion, and might not always be honored.

If the source image is larger than the maximum size, it will automatically be scaled down to the upper size limits.

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.MaxLineCount
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.TextRecognizerOptions.MaxLineCount property

<!--
public uint MaxLineCount { get; set; }
-->

Gets or sets the maximum number of lines to return from the recognition operation.

##### Property value

The maximum number of lines to return from the recognition operation.

##### Remarks

Defaults to MaxLineCountSupported. If specified, the maximum lines returned will be the lesser of this value and MaxLineCountSupported.

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.TextRecognizerOptions.OrientationDetection
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.TextRecognizerOptions.OrientationDetection property

<!--
public Microsoft.Windows.Vision.OrientationDetectionOptions OrientationDetection { get; set; }
-->

Gets or sets whether to detect the text orientation.

##### Property value

Whether to detect the text orientation. Default value is None.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.Vision.TextRecognizerOptions.#ctor
-api-type: winrt constructor
--->

#### Microsoft.Windows.Vision.TextRecognizerOptions.#ctor constructor

<!--
public TextRecognizerOptions ();
-->

Initializes a new instance of the TextRecognizerOptions class.

##### Remarks

##### See also

##### Examples


---


<!---
-api-id: N:Microsoft.Windows.Imaging
-api-type: winrt namespace
--->

## Microsoft.Windows.Imaging namespace

Provides APIs for machine learning models that manipulate images.

### Remarks

### See also

### Examples

<!---
-api-id: T:Microsoft.Windows.Imaging.ImageBuffer
-api-type: winrt class
--->

### Microsoft.Windows.Imaging.ImageBuffer class

<!--
public sealed class ImageBuffer : System.IDisposable
-->

Represents an uncompressed bitmap for efficient cross-process marshaling.

#### Remarks

ImageBuffer can be used with AI model APIs such as TextRecognizer that require image data. Typical usage involves creating an ImageBuffer from an existing SoftwareBitmap.

#### See also

#### Examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Buffer
-api-type: winrt property
--->

#### Microsoft.Windows.Imaging.ImageBuffer.Buffer property

<!--
public Windows.Storage.Streams.IBuffer Buffer { get; }
-->

Gets the current image buffer.

##### Property value

The current image buffer.

##### Remarks

##### See also

##### Examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.BufferLength
-api-type: winrt property
--->

#### Microsoft.Windows.Imaging.ImageBuffer.BufferLength property

<!--
public uint BufferLength { get; }
-->

Gets the length of the image buffer.

##### Property value

The length of the image buffer.

##### Remarks

##### See also

##### Examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.Close
-api-type: winrt method
--->

#### Microsoft.Windows.Imaging.ImageBuffer.Close method

<!--
// This member is not implemented in C#
-->

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.

##### See also

##### Examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CopyToBuffer(System.Byte[])
-api-type: winrt method
--->

#### Microsoft.Windows.Imaging.ImageBuffer.CopyToBuffer(System.Byte[]) method

<!--
public void CopyToBuffer (byte[] values);
-->

Copies the current buffer into the provided target buffer.

##### Parameters

###### values

Vector of bytes in the buffer.

##### Remarks

##### See also

##### Examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Windows.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap) method

<!--
public static Microsoft.Windows.Imaging.ImageBuffer CreateBufferAttachedToBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
-->

Create a new ImageBuffer from an existing SotftwareBitmap by getting an IMemoryBufferReference from the bitmap object.

##### Parameters

###### softwareBitmap

The SotftwareBitmap to create the ImageBuffer from.

##### Returns

The ImageBuffer or null if it's an unsupported format.

##### Remarks

The SoftwareBitmap is locked until the async operation completes and the new ImageBuffer is destroyed. 

##### See also

##### Examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

#### Microsoft.Windows.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap) method

<!--
public static Microsoft.Windows.Imaging.ImageBuffer CreateCopyFromBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
-->

Create a new ImageBuffer from an existing SotftwareBitmap by copying out the underlying bitmap data.

##### Parameters

###### softwareBitmap

The SotftwareBitmap to create the ImageBuffer from.

##### Returns

The ImageBuffer or null if it's an unsupported format.

##### Remarks

The SoftwareBitmap is locked until the async operation completes and the new ImageBuffer is destroyed.

##### See also

##### Examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateSoftwareBitmap
-api-type: winrt method
--->

#### Microsoft.Windows.Imaging.ImageBuffer.CreateSoftwareBitmap method

<!--
public Windows.Graphics.Imaging.SoftwareBitmap CreateSoftwareBitmap ();
-->

Create a new SoftwareBitmap of pixel type BGRA32 from the pixel data stored in an ImageBuffer.

##### Returns

The new SoftwareBitmap of pixel type BGRA32.

##### Remarks

##### See also

##### Examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Height
-api-type: winrt property
--->

#### Microsoft.Windows.Imaging.ImageBuffer.Height property

<!--
public uint Height { get; }
-->

Gets the height of the image, in pixels.

##### Property value

The height of the image, in pixels.

##### Remarks

##### See also

##### Examples




<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Windows.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt constructor
--->

#### Microsoft.Windows.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer, Microsoft.Windows.Imaging.PixelFormat, System.UInt32, System.UInt32) constructor

<!--
public ImageBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Windows.Imaging.PixelFormat pixelFormat, uint width, uint height);
-->

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

##### Remarks

##### See also

##### Examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.PixelFormat
-api-type: winrt property
--->

#### Microsoft.Windows.Imaging.ImageBuffer.PixelFormat property

<!--
public Microsoft.Windows.Imaging.PixelFormat PixelFormat { get; }
-->

Gets the pixel format of the image.

##### Property value

The pixel format of the image.

##### Remarks

##### See also

##### Examples




<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Width
-api-type: winrt property
--->

#### Microsoft.Windows.Imaging.ImageBuffer.Width property

<!--
public uint Width { get; }
-->

Gets the width of the image, in pixels.

##### Property value

The width of the image, in pixels.

##### Remarks

##### See also

##### Examples



<!---
-api-id: T:Microsoft.Windows.Imaging.PixelFormat
-api-type: winrt enum
--->

### Microsoft.Windows.Imaging.PixelFormat enum

<!--
public enum PixelFormat
-->

Specifies the types of binary layouts for the underlying bitmap data.

#### Enum fields

##### Undefined: 0

Binary format is undefined.

##### Rgb24: 1

The binary format is 24 bits per pixel; 8 bits each are used for the red, green, and blue components.

##### Argb32: 2

The binary format 32 bits per pixel; 8 bits each are used for the alpha, red, green, and blue components.

##### Rgba32: 3

The binary format is 32 bits per pixel; 8 bits each are used for the red, green, blue, and alpha components. The color components are stored in red, green, blue, and alpha order.

##### Bgra32: 4

The binary format is 32 bits per pixel; 8 bits each are used for the blue, green, red, and alpha components. The color components are stored in blue, green, red, and alpha order.

##### Gray8: 5

The binary format is 16 bits per pixel. The color information specifies 65536 shades of gray.

#### Remarks

#### See also

#### Examples
