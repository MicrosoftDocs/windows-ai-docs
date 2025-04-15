---
title: API ref for Phi Silica in the Windows App SDK
description: Learn about the Windows App SDK APIs that can access local language models such as Phi Silica, Microsoft's most powerful NPU-tuned language model that enables on-device processing and generation of chat, reasoning over text, math solving, code generation, and more.
ms.topic: article
ms.date: 04/14/2025
---

# API ref for Phi Silica in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.
>
> - Phi Silica is not available in mainland China.
> - Self-contained apps are not supported.

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/) APIs that can access local language models such as Phi Silica, Microsoft's most powerful NPU-tuned local language model that enables on-device processing and generation of chat, reasoning over text, math solving, code generation, and more.

For more details, see [Get started with Phi Silica in the Windows App SDK](phi-silica.md).

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Phi Silica** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

---

<!---
-api-id: N:Microsoft.Windows.AI
-api-type: winrt namespace
--->

## Microsoft.Windows.AI namespace

Provides APIs for local, on-device AI features.



<!---
-api-id: T:Microsoft.Windows.AI.AIFeatureReadyResult
-api-type: winrt class
--->

### AIFeatureReadyResult class

```
public sealed class AIFeatureReadyResult
```


<!---
-api-id: P:Microsoft.Windows.AI.AIFeatureReadyResult.Error
-api-type: winrt property
--->

#### AIFeatureReadyResult.Error property

```
public System.Exception Error { get; }
```

##### Property value

<!---
-api-id: P:Microsoft.Windows.AI.AIFeatureReadyResult.ErrorDisplayText
-api-type: winrt property
--->

#### AIFeatureReadyResult.ErrorDisplayText property

```
public string ErrorDisplayText { get; }
```

##### Property value

<!---
-api-id: P:Microsoft.Windows.AI.AIFeatureReadyResult.ExtendedError
-api-type: winrt property
--->

#### AIFeatureReadyResult.ExtendedError property

```
public System.Exception ExtendedError { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.AIFeatureReadyResult.Status
-api-type: winrt property
--->

#### AIFeatureReadyResult.Status property

```
public Microsoft.Windows.AI.AIFeatureReadyResultState Status { get; }
```

##### Property value


<!---
-api-id: T:Microsoft.Windows.AI.AIFeatureReadyResultState
-api-type: winrt enum
--->

### AIFeatureReadyResultState enumeration

```
public enum AIFeatureReadyResultState
```

#### Fields

##### InProgress: 0

##### Success: 1

##### Failure: 2


<!---
-api-id: T:Microsoft.Windows.AI.AIFeatureReadyState
-api-type: winrt enum
--->

### AIFeatureReadyState enumeration

```
public enum AIFeatureReadyState
```

#### Fields

##### Ready: 0

##### EnsureNeeded: 1

##### NotSupportedOnCurrentSystem: 2

##### DisabledByUser: 3



<!---
-api-id: N:Microsoft.Windows.AI.Generative
-api-type: winrt namespace
--->

## Microsoft<wbr>.Windows<wbr>.AI<wbr>.Generative namespace

Provides APIs for local, on-device generative AI prompt processing and responses.




<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator
-api-type: winrt class
--->

### ImageDescriptionGenerator class

```
public sealed class ImageDescriptionGenerator : System.IDisposable
```

<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.Close
-api-type: winrt method
--->

#### ImageDescriptionGenerator<wbr>.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.CreateAsync
-api-type: winrt method
--->

#### ImageDescriptionGenerator<wbr>.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.ImageDescriptionGenerator> CreateAsync ();
```





<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionKind,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionKind,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.ImageDescriptionResult,string> DescribeAsync (Microsoft.Graphics.Imaging.ImageBuffer image, Microsoft.Windows.AI.Generative.ImageDescriptionKind kind, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```

##### Parameters

###### image

###### kind

###### contentFilterOptions

##### Returns




<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.EnsureReadyAsync
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.EnsureReadyAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.AIFeatureReadyResult,double> EnsureReadyAsync ();
```

##### Returns



<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.GetReadyState
-api-type: winrt method
--->

#### ImageDescriptionGenerator.GetReadyState method

```
public static Microsoft.Windows.AI.AIFeatureReadyState GetReadyState ();
```
##### Returns


<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageDescriptionKind
-api-type: winrt enum
--->

### ImageDescriptionKind enumeration

```
public enum ImageDescriptionKind
```

#### Fields

##### BriefDescription: 0

##### DetailedDescrition: 1

##### DiagramDescription: 2

##### AccessibleDescription: 3



<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageDescriptionResult
-api-type: winrt class
--->

### Microsoft.Windows.AI.Generative.ImageDescriptionResult class

```
public sealed class ImageDescriptionResult
```


<!---
-api-id: P:Microsoft.Windows.AI.Generative.ImageDescriptionResult.Description
-api-type: winrt property
--->

#### ImageDescriptionResult.Description property

```
public string Description { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.Generative.ImageDescriptionResult.Status
-api-type: winrt property
--->

#### ImageDescriptionResult.Status property

```
public Microsoft.Windows.AI.Generative.ImageDescriptionResultStatus Status { get; }
```

##### Property value




<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageDescriptionResultStatus
-api-type: winrt enum
--->

#### ImageDescriptionResultStatus enumeration

```
public enum ImageDescriptionResultStatus
```

#### Fields

##### Complete: 0

##### InProgress: 1

##### BlockedByPolicy: 2

##### ImageBlockedByContentModeration: 3

##### TextInImageBlockedByContentModeration: 4

##### DescriptionTextBlockedByContentModeration: 5

##### ImageHasTooMuchText: 6

##### InternalError: 7











<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModel
-api-type: winrt class
--->

### LanguageModel class

```
public sealed class LanguageModel : System.IDisposable
```

Represents an object that can interact with a local language model to generate responses for a provided prompt.



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.Close
-api-type: winrt method
--->

#### LanguageModel<wbr>.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateAsync
-api-type: winrt method
--->

#### LanguageModel<wbr>.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModel> CreateAsync ();
```

Asynchronously creates a new instance of the LanguageModel class.

##### Returns

A new instance of the TextRecognizer class.



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateContext
-api-type: winrt method
--->

#### LanguageModel<wbr>.CreateContext method

```
public Microsoft.Windows.AI.Generative.LanguageModelContext CreateContext ();
```


##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateContext(System.String)
-api-type: winrt method
--->

#### LanguageModel.CreateContext(System.String) method

```
public Microsoft.Windows.AI.Generative.LanguageModelContext CreateContext (string systemPrompt);
```

##### Parameters

###### systemPrompt

##### Returns

##### Remarks


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateContext(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel<wbr>.CreateContext(System<wbr>.String,Microsoft<wbr>.Windows<wbr>.AI<wbr>.ContentModeration<wbr>.ContentFilterOptions) method

```
public Microsoft.Windows.AI.Generative.LanguageModelContext CreateContext (string systemPrompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### systemPrompt

###### contentFilterOptions

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.EnsureReadyAsync
-api-type: winrt method
--->

#### LanguageModel<wbr>.EnsureReadyAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.AIFeatureReadyResult,double> EnsureReadyAsync ();
```

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVectors(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel<wbr>.GenerateEmbeddingVectors(System<wbr>.String,Microsoft<wbr>.Windows<wbr>.AI<wbr>.ContentModeration<wbr>.ContentFilterOptions) method

```
public System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> GenerateEmbeddingVectors (string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### prompt

###### contentFilterOptions

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVectors(System.String)
-api-type: winrt method
--->

#### LanguageModel<wbr>.GenerateEmbeddingVectors(System<wbr>.String) method

```
public Microsoft.Windows.AI.Generative.LanguageModelEmbeddingVectorResult GenerateEmbeddingVectors (string prompt);
```

##### Parameters

###### prompt

##### Returns

##### Remarks






<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String,Microsoft.Windows.AI.Generative.LanguageModelOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String,Microsoft.Windows.AI.Generative.LanguageModelOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseAsync (Microsoft.Windows.AI.Generative.LanguageModelContext context, string prompt, Microsoft.Windows.AI.Generative.LanguageModelOptions options);
```

##### Parameters

###### context

###### prompt

###### options

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(System.String) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseAsync (string prompt);
```

##### Parameters

###### prompt

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(System.String,Microsoft.Windows.AI.Generative.LanguageModelOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(System.String,Microsoft.Windows.AI.Generative.LanguageModelOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseAsync (string prompt, Microsoft.Windows.AI.Generative.LanguageModelOptions options);
```

##### Parameters

###### prompt

###### options

##### Returns




<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsAsync(Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector})
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsAsync(Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector}) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseFromEmbeddingsAsync (System.Collections.Generic.IEnumerable<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding);
```

##### Parameters

###### promptEmbedding

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsAsync(Microsoft.Windows.AI.Generative.LanguageModelContext,Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.Generative.LanguageModelOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsAsync(Microsoft.Windows.AI.Generative.LanguageModelContext,Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.Generative.LanguageModelOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseFromEmbeddingsAsync (Microsoft.Windows.AI.Generative.LanguageModelContext context, System.Collections.Generic.IEnumerable<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding, Microsoft.Windows.AI.Generative.LanguageModelOptions options);
```

##### Parameters

###### context

###### promptEmbedding

###### options

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsAsync(Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.Generative.LanguageModelOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsAsync(Windows.Foundation.Collections.IIterable{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.Generative.LanguageModelOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponseResult,string> GenerateResponseFromEmbeddingsAsync (System.Collections.Generic.IEnumerable<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding, Microsoft.Windows.AI.Generative.LanguageModelOptions options);
```

##### Parameters

###### promptEmbedding

###### options

##### Returns







<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GetReadyState
-api-type: winrt method
--->

#### LanguageModel.GetReadyState method

```
public static Microsoft.Windows.AI.AIFeatureReadyState GetReadyState ();
```

##### Returns




<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GetUsablePromptLength(System.String)
-api-type: winrt method
--->

#### LanguageModel.GetUsablePromptLength(System.String) method

```
public ulong GetUsablePromptLength (string prompt);
```

##### Parameters

###### prompt

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GetUsablePromptLength(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String)
-api-type: winrt method
--->

#### LanguageModel.GetUsablePromptLength(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String) method

```
public ulong GetUsablePromptLength (Microsoft.Windows.AI.Generative.LanguageModelContext context, string prompt);
```

##### Parameters

###### context

###### prompt

##### Returns


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GetVectorSpaceId
-api-type: winrt method
--->

#### LanguageModel.GetVectorSpaceId method

```
public Guid GetVectorSpaceId ();
```

##### Returns





<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelContext
-api-type: winrt class
--->

### LanguageModelContext class

```
public sealed class LanguageModelContext
```

<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModelContext.Close
-api-type: winrt method
--->

#### LanguageModelContext.Close method

```
// This member is not implemented in C#
```

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelEmbeddingVectorResult
-api-type: winrt class
--->

### LanguageModelEmbeddingVectorResult class

```
public sealed class LanguageModelEmbeddingVectorResult
```

<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelEmbeddingVectorResult.EmbeddingVectors
-api-type: winrt property
--->

#### LanguageModelEmbeddingVectorResult.EmbeddingVectors property

```
public System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> EmbeddingVectors { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelEmbeddingVectorResult.ExtendedError
-api-type: winrt property
--->

#### LanguageModelEmbeddingVectorResult.ExtendedError property

```
public System.Exception ExtendedError { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelEmbeddingVectorResult.Status
-api-type: winrt property
--->

#### LanguageModelEmbeddingVectorResult.Status property

```
public Microsoft.Windows.AI.Generative.LanguageModelResponseStatus Status { get; }
```

##### Property value


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelOptions
-api-type: winrt class
--->

### LanguageModelOptions class

```
public sealed class LanguageModelOptions
```





<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModelOptions.#ctor
-api-type: winrt constructor
--->

#### LanguageModelOptions<wbr>.#ctor constructor

```
public LanguageModelOptions ();
```


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.ContentFilterOptions
-api-type: winrt property
--->

#### LanguageModelOptions.ContentFilterOptions property

```
public Microsoft.Windows.AI.ContentModeration.ContentFilterOptions ContentFilterOptions { get; set; }
```

##### Property value




<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.Temperature
-api-type: winrt property
--->

#### LanguageModelOptions<wbr>.Temperature property

```
public float Temperature { get; set; }
```


##### Property value




<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.TopK
-api-type: winrt property
--->

#### LanguageModelOptions<wbr>.TopK property

```
public uint TopK { get; set; }
```


##### Property value




<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.TopP
-api-type: winrt property
--->

#### LanguageModelOptions<wbr>.TopP property

```
public float TopP { get; set; }
```


##### Property value





<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponseResult
-api-type: winrt class
--->

### Microsoft.Windows.AI.Generative.LanguageModelResponseResult class

```
public sealed class LanguageModelResponseResult
```

<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponseResult.ExtendedError
-api-type: winrt property
--->

#### Microsoft.Windows.AI.Generative.LanguageModelResponseResult.ExtendedError property

```
public System.Exception ExtendedError { get; }
```

##### Property value


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponseResult.Status
-api-type: winrt property
--->

#### Microsoft.Windows.AI.Generative.LanguageModelResponseResult.Status property

```
public Microsoft.Windows.AI.Generative.LanguageModelResponseStatus Status { get; }
```

##### Property value

<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponseResult.Text
-api-type: winrt property
--->

#### Microsoft.Windows.AI.Generative.LanguageModelResponseResult.Text property

```
public string Text { get; }
```

##### Property value




<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponseStatus
-api-type: winrt enum
--->

### LanguageModelResponseStatus enumeration

```
public enum LanguageModelResponseStatus
```


#### Fields

##### Complete: 0

##### InProgress: 1

##### BlockedByPolicy: 2

##### PromptLargerThanContext: 3

##### PromptBlockedByPolicy: 4

##### ResponseBlockedByPolicy: 5










## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Get started with Phi Silica in the Windows App SDK](phi-silica.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
