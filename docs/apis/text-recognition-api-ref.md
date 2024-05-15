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

<!---
-api-id: N:Microsoft.Windows.Vision
-api-type: winrt namespace
--->

## Microsoft.Windows.Vision

Provides APIs for machine learning models that analyze the textual content of images.

### Remarks

### See also

### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.BoundingBox
-api-type: winrt struct
--->

### Microsoft.Windows.Vision.TextRecognition.BoundingBox

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

:::image type="content" source="images/bounding-box-examples.png" alt-text="Diagram of three bounding box examples showing how corner points are identified based on text rotation.":::

#### Examples

#### See also


<!---
-api-id: T:Microsoft.Windows.Vision.DetectedLineStyle
-api-type: winrt enum
--->

### Microsoft.Windows.Vision.DetectedLineStyle

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

### Microsoft.Windows.Vision.OrientationDetectionOptions

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

### Microsoft.Windows.Vision.RecognizedLine

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

#### Microsoft.Windows.Vision.RecognizedLine.Style

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

#### Microsoft.Windows.Vision.RecognizedLine.Text

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

#### Microsoft.Windows.Vision.RecognizedLine.Words

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

### Microsoft.Windows.Vision.RecognizedLineStyle

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

### Microsoft.Windows.Vision.RecognizedText

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

#### Microsoft.Windows.Vision.RecognizedText.ImageAngle

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

#### Microsoft.Windows.Vision.RecognizedText.Lines

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

### Microsoft.Windows.Vision.RecognizedWord

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

#### Microsoft.Windows.Vision.RecognizedWord.BoundingBox

<!--
public Microsoft.Windows.Vision.BoundingBox BoundingBox { get; }
-->

Gets the quadrilateral boundary of the recognized word.

##### Property-value

The quadrilateral boundary of the recognized word. TopLeft is relative to the word's rotation.

##### Remarks

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Confidence
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedWord.Confidence

<!--
public float Confidence { get; }
-->

Gets how likely this word was recognized correctly.

##### property-value

Wow likely this word was recognized correctly. Value ranges from 0.0 to 1.0, inclusive.

##### Remarks

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.Vision.RecognizedWord.Text
-api-type: winrt property
--->

#### Microsoft.Windows.Vision.RecognizedWord.Text

<!--
public string Text { get; }
-->

Gets the text of the recognized word.

##### property-value

The text of the recognized word.

##### Remarks

##### See also

##### Examples


<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognizer
-api-type: winrt class
--->

### Microsoft.Windows.Vision.TextRecognition.TextRecognizer

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

#### Microsoft.Windows.Vision.TextRecognizer.Close

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

#### Microsoft.Windows.Vision.TextRecognizer.CreateAsync

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

#### Microsoft.Windows.Vision.TextRecognizer.IsAvailable

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

#### Microsoft.Windows.Vision.TextRecognizer.MakeAvailableAsync

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

#### Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImage(Microsoft.Windows.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)

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

#### Microsoft.Windows.Vision.TextRecognizer.RecognizeTextFromImageAsync(Microsoft.Windows.Imaging.ImageBuffer,Microsoft.Windows.Vision.TextRecognizerOptions)

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

### Microsoft.Windows.Vision.TextRecognizerOptions

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

#### Microsoft.Windows.Vision.TextRecognizerOptions.MaxAnalysisSize

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

# Microsoft.Windows.Vision.TextRecognizerOptions.MaxLineCount

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

#### Microsoft.Windows.Vision.TextRecognizerOptions.OrientationDetection

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

#### Microsoft.Windows.Vision.TextRecognizerOptions.#ctor

<!--
public TextRecognizerOptions ();
-->

Initializes a new instance of the TextRecognizerOptions class.

##### Remarks

##### See also

##### Examples






<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.RecognizedWord
-api-type: winrt class
--->

<!-- Class syntax.
public class Word : Microsoft.Windows.Vision.TextRecognition.IRecognizedWord
-->

### Microsoft.Windows.Vision.TextRecognition.RecognizedWord class

A single recognized word.

**Remarks:**

**Examples:**

**See also:**

BoundingBox

Quadrilateral boundary of the recognized word. TopLeft is relative to the word's rotation.

Confidence

How likely this word was recognized correctly, from 0.0 to 1.0.

Text

The text of the recognized word.

<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.RecognizedLine
-api-type: winrt class
--->

<!-- Class syntax.
public class RecognizedLine : Microsoft.Windows.Vision.TextRecognition.IRecognizedLine
-->

### Microsoft.Windows.Vision.TextRecognition.RecognizedLine class

A single recognized line of text.

**Remarks:**

**Examples:**

**See also:**

Text

The text of the recognized line. Same as all of the words concatenated with spaces.

BoundingBox

Quadrilateral boundary of the recognized line. TopLeft is relative to the line's rotation.

Words

Recognized words in this line.

Style

The recognized line style. Includes whether the line of text was handwritten or not, and with what level of confidence.

<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.RecognizedText
-api-type: winrt class
--->

<!-- Class syntax.
public class RecognizedText : Microsoft.Windows.Vision.TextRecognition.IRecognizedText
-->

### Microsoft.Windows.Vision.TextRecognition.RecognizedText class

The result of an image-to-text recognition operation.

**Remarks:**

**Examples:**

**See also:**

Lines

Collection of recognized lines.

ImageAngle

Overall rotational angle of the recognized text in clockwise degrees.



<!---
-api-id: T:Microsoft.Windows.Vision.TextRecognition.TextRecognizerOptions
-api-type: winrt class
--->

<!-- Class syntax.
public class TextRecognizerOptions : Microsoft.Windows.Vision.TextRecognition.ITextRecognizerOptions
-->

### Microsoft.Windows.Vision.TextRecognition.TextRecognizerOptions class

Options for configuring the text recognition model in TextRecognizer.

MaxAnalysisSize

Callers can provide a hint for the maximum size image to use when detecting text. If the source image is larger than the maximum size, it will automatically be scaled down to the upper size limits. This size is a suggestion, and might not always be honored. Default value is 1152 width and 768 height.

Spec note: The max size is always honored when processing on CPU. QNN models do not support dynamically sized layers at this time. As a result, when running on NPU hardware, the size of the image used during analysis might be snapped to a fixed size, rather than using the source or limit sizes. Details TBD once an NPU-tailored model is available.

OrientationDetection

Default value is None. Spec note: Need to double-check what this does in practice.

MaxLineCount

Maximum number of lines to return from the recognition operation. Defaults to MaxLineCountSupported. If specified, the maximum lines returned will be the lesser of this value and MaxLineCountSupported.

MaxLineCountSupported

Maximum number of lines supported by the current model.



<!---
-api-id: N:Microsoft.Windows.Imaging 
-api-type: winrt namespace
--->

## Microsoft.Windows.Vision.TextRecognition namespace

Provides APIs for machine learning models that analyze the textual content of images.

**Remarks:**

**See also:**




<!---
-api-id: N:Microsoft.Windows.Imaging
-api-type: winrt namespace
--->

## Microsoft.Windows.Imaging

### -description

### -remarks

### -see-also

### -examples

<!---
-api-id: T:Microsoft.Windows.Imaging.ImageBuffer
-api-type: winrt class
--->

## Microsoft.Windows.Imaging.ImageBuffer

<!--
public sealed class ImageBuffer : System.IDisposable
-->

### -description

### -remarks

### -see-also

### -examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Buffer
-api-type: winrt property
--->

## Microsoft.Windows.Imaging.ImageBuffer.Buffer

<!--
public Windows.Storage.Streams.IBuffer Buffer { get; }
-->

### -description

### -property-value

### -remarks

### -see-also

### -examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.BufferLength
-api-type: winrt property
--->

## Microsoft.Windows.Imaging.ImageBuffer.BufferLength

<!--
public uint BufferLength { get; }
-->

### -description

### -property-value

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.Close
-api-type: winrt method
--->

## Microsoft.Windows.Imaging.ImageBuffer.Close

<!--
// This member is not implemented in C#
-->

### -description

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CopyToBuffer(System.Byte[])
-api-type: winrt method
--->

## Microsoft.Windows.Imaging.ImageBuffer.CopyToBuffer(System.Byte[])

<!--
public void CopyToBuffer (byte[] values);
-->

### -description

### -parameters

#### -param values

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

## Microsoft.Windows.Imaging.ImageBuffer.CreateBufferAttachedToBitmap(Windows.Graphics.Imaging.SoftwareBitmap)

<!--
public static Microsoft.Windows.Imaging.ImageBuffer CreateBufferAttachedToBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
-->

### -description

### -parameters

#### -param softwareBitmap

### -returns

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap)
-api-type: winrt method
--->

## Microsoft.Windows.Imaging.ImageBuffer.CreateCopyFromBitmap(Windows.Graphics.Imaging.SoftwareBitmap)

<!--
public static Microsoft.Windows.Imaging.ImageBuffer CreateCopyFromBitmap (Windows.Graphics.Imaging.SoftwareBitmap softwareBitmap);
-->

### -description

### -parameters

#### -param softwareBitmap

### -returns

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.CreateSoftwareBitmap
-api-type: winrt method
--->

## Microsoft.Windows.Imaging.ImageBuffer.CreateSoftwareBitmap

<!--
public Windows.Graphics.Imaging.SoftwareBitmap CreateSoftwareBitmap ();
-->


### -description

### -returns

### -remarks

### -see-also

### -examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Height
-api-type: winrt property
--->

## Microsoft.Windows.Imaging.ImageBuffer.Height

<!--
public uint Height { get; }
-->

### -description

### -property-value

### -remarks

### -see-also

### -examples



<!---
-api-id: M:Microsoft.Windows.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Windows.Imaging.PixelFormat,System.UInt32,System.UInt32)
-api-type: winrt constructor
--->

## Microsoft.Windows.Imaging.ImageBuffer.#ctor(Windows.Storage.Streams.IBuffer,Microsoft.Windows.Imaging.PixelFormat,System.UInt32,System.UInt32)

<!--
public ImageBuffer (Windows.Storage.Streams.IBuffer buffer, Microsoft.Windows.Imaging.PixelFormat pixelFormat, uint width, uint height);
-->

### -description

### -parameters

#### -param buffer

#### -param pixelFormat

#### -param width

#### -param height

### -remarks

### -see-also

### -examples



<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.PixelFormat
-api-type: winrt property
--->

## Microsoft.Windows.Imaging.ImageBuffer.PixelFormat

<!--
public Microsoft.Windows.Imaging.PixelFormat PixelFormat { get; }
-->

### -description

### -property-value

### -remarks

### -see-also

### -examples




<!---
-api-id: P:Microsoft.Windows.Imaging.ImageBuffer.Width
-api-type: winrt property
--->

## Microsoft.Windows.Imaging.ImageBuffer.Width

<!--
public uint Width { get; }
-->

### -description

### -property-value

### -remarks

### -see-also

### -examples



<!---
-api-id: T:Microsoft.Windows.Imaging.PixelFormat
-api-type: winrt enum
--->

## Microsoft.Windows.Imaging.PixelFormat

<!--
public enum PixelFormat
-->

### -description

### -enum-fields

#### -field Undefined: 0

#### -field Rgb24: 1

#### -field Argb32: 2

#### -field Rgba32: 3

#### -field Bgra32: 4

#### -field Gray8: 5

### -remarks

### -see-also

### -examples
