---
title: API ref for Phi Silica in the Windows App SDK
description: Learn about the Windows App SDK APIs that can access local language models such as Phi Silica, Microsoft's most powerful NPU-tuned language model that enables on-device processing and generation of chat, reasoning over text, math solving, code generation, and more.
ms.topic: article
ms.date: 01/17/2025
ms.author: kbridge
author: karl-bridge-microsoft
---

# API ref for Phi Silica in the Windows App SDK

> [!IMPORTANT]
> **Available in the latest [experimental channel](/windows/apps/windows-app-sdk/experimental-channel) release of the Windows App SDK.**
>
> The Windows App SDK experimental channel includes APIs and features in early stages of development. All APIs in the experimental channel are subject to extensive revisions and breaking changes and may be removed from subsequent releases at any time. Experimental features are not supported for use in production environments and apps that use them cannot be published to the Microsoft Store.

Learn about the [Windows App SDK](/windows/apps/windows-app-sdk/) APIs that can access local language models such as Phi Silica, Microsoft's most powerful NPU-tuned local language model that enables on-device processing and generation of chat, reasoning over text, math solving, code generation, and more.

For more details, see [Get started with Phi Silica in the Windows App SDK](phi-silica.md).

> [!TIP]
> Provide feedback on these APIs and their functionality by creating a [new Issue](https://github.com/microsoft/WindowsAppSDK/issues/new?template=Blank+issue) in the Windows App SDK GitHub repo (include **Phi Silica** in the title) or by responding to an [existing issue](https://github.com/microsoft/WindowsAppSDK/issues).

---

<!---
-api-id: N:Microsoft.Windows.AI.Generative
-api-type: winrt namespace
--->

## Microsoft.Windows.AI.Generative namespace

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

#### ImageDescriptionGenerator.Close method

```
// This member is not implemented in C#
```


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.CreateAsync
-api-type: winrt method
--->

#### ImageDescriptionGenerator.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.ImageDescriptionGenerator> CreateAsync ();
```

<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.DescribeAsync(Microsoft.Windows.Imaging.ImageBuffer)
-api-type: winrt method
--->

#### ImageDescriptionGenerator.DescribeAsync(Microsoft.Windows.Imaging.ImageBuffer) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> DescribeAsync (Microsoft.Windows.Imaging.ImageBuffer image);
```

> [!WARNING]
> When calling `ImageDescriptionGenerator.DescribeAsync()` on an image, sometimes an error is thrown. This error can be skipped, allowing the debugger to continue and generate correct output. The error is only visible in the developer environment, not for end users (customers using your app). Using Debug or Release builds will trigger this error. The error appears intermittently and not on every run.


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionScenario)
-api-type: winrt method
--->

#### ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionScenario) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> DescribeAsync (Microsoft.Graphics.Imaging.ImageBuffer image, Microsoft.Windows.AI.Generative.ImageDescriptionScenario scenario);
```

##### Parameters

###### image

###### scenario

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionScenario,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### ImageDescriptionGenerator.DescribeAsync(Microsoft.Graphics.Imaging.ImageBuffer,Microsoft.Windows.AI.Generative.ImageDescriptionScenario,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> DescribeAsync (Microsoft.Graphics.Imaging.ImageBuffer image, Microsoft.Windows.AI.Generative.ImageDescriptionScenario scenario, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### image

###### scenario

###### contentFilterOptions

##### Returns

##### Remarks




<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.IsAvailable
-api-type: winrt method
--->

#### ImageDescriptionGenerator.IsAvailable method

```
public static bool IsAvailable ();
```

<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageDescriptionGenerator.MakeAvailableAsync
-api-type: winrt method
--->

#### ImageDescriptionGenerator.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```


<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageDescriptionScenario
-api-type: winrt enum
--->

### ImageDescriptionScenario enumerator

```
public enum ImageDescriptionScenario
```

#### -description

#### -enum-fields

##### -field Accessibility: 1

##### -field Caption: 2

##### -field DetailedNarration: 3

##### -field OfficeCharts: 4

#### -remarks

#### -see-also

#### -examples


<!---
-api-id: T:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator
-api-type: winrt class
--->

### ImageLLMAdapterCreator class

```
public sealed class ImageLLMAdapterCreator : System.IDisposable
```


#### -description

### -remarks

#### -see-also

#### -examples








<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.Close
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.Close method

```
// This member is not implemented in C#
```


##### -description

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.CreateAsync
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.CreateAsync method

```
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator> CreateAsync ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.GetImageLLMEmbeddings(Microsoft.Windows.SemanticSearch.EmbeddingVector)
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.GetImageLLMEmbeddings(Microsoft.Windows.SemanticSearch.EmbeddingVector) method

```
public System.Collections.Generic.IReadOnlyList<float> GetImageLLMEmbeddings (Microsoft.Windows.SemanticSearch.EmbeddingVector embeddings);
```


##### -description

##### -parameters

###### -param embeddings

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.GetImageLLMEmbeddingsAsync(Microsoft.Windows.SemanticSearch.EmbeddingVector)
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.GetImageLLMEmbeddingsAsync(Microsoft.Windows.SemanticSearch.EmbeddingVector) method

```
public Windows.Foundation.IAsyncOperation<System.Collections.Generic.IReadOnlyList<float>> GetImageLLMEmbeddingsAsync (Microsoft.Windows.SemanticSearch.EmbeddingVector embeddings);
```


##### -description

##### -parameters

###### -param embeddings

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.GetModelInputSize
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.GetModelInputSize method

```
public uint GetModelInputSize ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.GetModelOutputSize
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.GetModelOutputSize method

```
public uint GetModelOutputSize ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.IsAvailable
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.IsAvailable method

```
public static bool IsAvailable ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.ImageLLMAdapterCreator.MakeAvailableAsync
-api-type: winrt method
--->

#### ImageLLMAdapterCreator.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples

















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

#### LanguageModel.Close method

<!--
// This member is not implemented in C#
-->

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateAsync
-api-type: winrt method
--->

#### LanguageModel.CreateAsync method

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

#### LanguageModel.CreateContext method

```
public Microsoft.Windows.AI.Generative.LanguageModelContext CreateContext ();
```


##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateContext(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.CreateContext(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Microsoft.Windows.AI.Generative.LanguageModelContext CreateContext (string systemPrompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### systemPrompt

###### contentFilterOptions

##### Returns

##### Remarks






<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVector(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateEmbeddingVector(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> GenerateEmbeddingVector (string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### prompt

###### contentFilterOptions

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVector(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateEmbeddingVector(System.String) method

```
public System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> GenerateEmbeddingVector (string prompt);
```


##### Parameters

###### prompt

##### Returns

##### Remarks




<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVectorAsync(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateEmbeddingVectorAsync(System.String) method

```
public Windows.Foundation.IAsyncOperation<System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector>> GenerateEmbeddingVectorAsync (string prompt);
```


##### Parameters

###### prompt

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateEmbeddingVectorAsync(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateEmbeddingVectorAsync(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperation<System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector>> GenerateEmbeddingVectorAsync (string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### prompt

###### contentFilterOptions

##### Returns

##### Remarks






<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModelResponse> GenerateResponseAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### options

###### prompt

###### contentFilterOptions

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(System.String) method

```
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModelResponse> GenerateResponseAsync (string prompt);
```

Generates and returns a complete response for a single prompt.

##### Parameters

###### prompt

A prompt in the form of a question.

##### Returns

A response string and status.

##### Exceptions

**ArgumentException**: The specified prompt is longer than the maximum number of tokens the model can accept.


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext) method

```
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModelResponse> GenerateResponseAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions, Microsoft.Windows.AI.Generative.LanguageModelContext context);
```


##### Parameters

###### options

###### prompt

###### contentFilterOptions

###### context

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String) method

```
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModelResponse> GenerateResponseAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt);
```


##### Parameters

###### options

###### prompt

##### Returns

##### Remarks








<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromEmbeddingsWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions, Microsoft.Windows.AI.Generative.LanguageModelContext context);
```


##### Parameters

###### options

###### promptEmbedding

###### contentFilterOptions

###### context

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromEmbeddingsWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### Parameters

###### options

###### promptEmbedding

###### contentFilterOptions

##### Returns

##### Remarks



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector})
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector}) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromEmbeddingsWithProgressAsync (System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding);
```


##### Parameters

###### promptEmbedding

##### Returns

##### Remarks

<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector})
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromEmbeddingsWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{Microsoft.Windows.SemanticSearch.EmbeddingVector}) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromEmbeddingsWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<Microsoft.Windows.SemanticSearch.EmbeddingVector> promptEmbedding);
```


##### -description

##### -parameters

###### -param options

###### -param promptEmbedding

##### -returns

##### -remarks

##### -see-also

##### -examples









<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64})
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64}) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromTokensWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<long> promptTokens);
```


##### -description

##### -parameters

###### -param options

###### -param promptTokens

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromTokensWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<long> promptTokens, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions, Microsoft.Windows.AI.Generative.LanguageModelContext context);
```


##### -description

##### -parameters

###### -param options

###### -param promptTokens

###### -param contentFilterOptions

###### -param context

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseFromTokensWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,Windows.Foundation.Collections.IVectorView{System.Int64},Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseFromTokensWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, System.Collections.Generic.IReadOnlyList<long> promptTokens, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### -description

##### -parameters

###### -param options

###### -param promptTokens

###### -param contentFilterOptions

##### -returns

##### -remarks

##### -see-also

##### -examples






<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### -description

##### -parameters

###### -param options

###### -param prompt

###### -param contentFilterOptions

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseWithProgressAsync(System.String) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseWithProgressAsync (string prompt);
```


##### -description

##### -parameters

###### -param prompt

## -returns

##### -remarks

##### -see-also

##### -examples



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt);
```


##### -description

##### -parameters

###### -param options

###### -param prompt

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext)
-api-type: winrt method
--->

#### LanguageModel.GenerateResponseWithProgressAsync(Microsoft.Windows.AI.Generative.LanguageModelOptions,System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions,Microsoft.Windows.AI.Generative.LanguageModelContext) method

```
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseWithProgressAsync (Microsoft.Windows.AI.Generative.LanguageModelOptions options, string prompt, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions, Microsoft.Windows.AI.Generative.LanguageModelContext context);
```


##### -description

##### -parameters

###### -param options

###### -param prompt

###### -param contentFilterOptions

###### -param context

##### -returns

##### -remarks

##### -see-also

##### -examples






<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateTokens(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateTokens(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public System.Collections.Generic.IReadOnlyList<long> GenerateTokens (string text, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### -description

##### -parameters

###### -param text

###### -param contentFilterOptions

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateTokens(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

### LanguageModel.GenerateTokens(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public System.Collections.Generic.IReadOnlyList<long> GenerateTokens (string text, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### -description

##### -parameters

###### -param text

###### -param contentFilterOptions

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateTokensAsync(System.String)
-api-type: winrt method
--->

#### LanguageModel.GenerateTokensAsync(System.String) method

```
public Windows.Foundation.IAsyncOperation<System.Collections.Generic.IReadOnlyList<long>> GenerateTokensAsync (string text);
```


##### -description

##### -parameters

###### -param text

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateTokensAsync(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions)
-api-type: winrt method
--->

#### LanguageModel.GenerateTokensAsync(System.String,Microsoft.Windows.AI.ContentModeration.ContentFilterOptions) method

```
public Windows.Foundation.IAsyncOperation<System.Collections.Generic.IReadOnlyList<long>> GenerateTokensAsync (string text, Microsoft.Windows.AI.ContentModeration.ContentFilterOptions contentFilterOptions);
```


##### -description

##### -parameters

###### -param text

###### -param contentFilterOptions

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.IsAvailable
-api-type: winrt method
--->

#### LanguageModel.IsAvailable method

```
public static bool IsAvailable ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.IsPromptLargerThanContext(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String)
-api-type: winrt method
--->

#### LanguageModel.IsPromptLargerThanContext(Microsoft.Windows.AI.Generative.LanguageModelContext,System.String) method

```
public bool IsPromptLargerThanContext (Microsoft.Windows.AI.Generative.LanguageModelContext context, string prompt);
```


##### -description

##### -parameters

###### -param context

###### -param prompt

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.IsPromptLargerThanContext(System.String)
-api-type: winrt method
--->

#### LanguageModel.IsPromptLargerThanContext(System.String) method

```
public bool IsPromptLargerThanContext (string prompt);
```


##### -description

##### -parameters

###### -param prompt

##### -returns

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.MakeAvailableAsync
-api-type: winrt method
--->

#### LanguageModel.MakeAvailableAsync method

```
public static Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.Management.Deployment.PackageDeploymentResult,Microsoft.Windows.Management.Deployment.PackageDeploymentProgress> MakeAvailableAsync ();
```


##### -description

##### -returns

##### -remarks

##### -see-also

##### -examples



<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelContext
-api-type: winrt class
--->

### LanguageModelContext class

```
public sealed class LanguageModelContext
```


#### -description

#### -remarks

#### -see-also

#### -examples



<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelOptions
-api-type: winrt class
--->

### LanguageModelOptions class

```
public sealed class LanguageModelOptions
```


#### -description

#### -remarks

#### -see-also

#### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModelOptions.#ctor
-api-type: winrt constructor
--->

#### LanguageModelOptions.#ctor constructor

```
public LanguageModelOptions ();
```


##### -description

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModelOptions.#ctor(Microsoft.Windows.AI.Generative.LanguageModelSkill,System.Single,System.Single,System.UInt32)
-api-type: winrt constructor
--->

#### LanguageModelOptions.#ctor(Microsoft.Windows.AI.Generative.LanguageModelSkill,System.Single,System.Single,System.UInt32) constructor

```
public LanguageModelOptions (Microsoft.Windows.AI.Generative.LanguageModelSkill skill, float temp, float top_p, uint top_k);
```


##### -description

##### -parameters

###### -param skill

###### -param temp

###### -param top_p

###### -param top_k

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.Skill
-api-type: winrt property
--->

#### LanguageModelOptions.Skill property

```
public Microsoft.Windows.AI.Generative.LanguageModelSkill Skill { get; set; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.Temp
-api-type: winrt property
--->

#### LanguageModelOptions.Temp property

```
public float Temp { get; set; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.Top_k
-api-type: winrt property
--->

#### LanguageModelOptions.Top_k property

```
public uint Top_k { get; set; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelOptions.Top_p
-api-type: winrt property
--->

#### LanguageModelOptions.Top_p property

```
public float Top_p { get; set; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples



<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponse
-api-type: winrt class
--->

### LanguageModelResponse class

```
public sealed class LanguageModelResponse
```


#### -description

#### -remarks

#### -see-also

#### -examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModelResponse.#ctor(System.String,Microsoft.Windows.AI.Generative.LanguageModelResponseStatus)
-api-type: winrt constructor
--->

#### LanguageModelResponse.#ctor(System.String,Microsoft.Windows.AI.Generative.LanguageModelResponseStatus) constructor

```
public LanguageModelResponse (string response, Microsoft.Windows.AI.Generative.LanguageModelResponseStatus status);
```


##### -description

##### -parameters

###### -param response

###### -param status

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponse.Response
-api-type: winrt property
--->

#### LanguageModelResponse.Response property

```
public string Response { get; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponse.Status
-api-type: winrt property
--->

#### LanguageModelResponse.Status property

```
public Microsoft.Windows.AI.Generative.LanguageModelResponseStatus Status { get; }
```


##### -description

##### -property-value

##### -remarks

##### -see-also

##### -examples


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponseStatus
-api-type: winrt enum
--->

### LanguageModelResponseStatus enumeration

```
public enum LanguageModelResponseStatus
```


#### -description

#### -enum-fields

##### -field Complete: 0

##### -field InProgress: 1

##### -field BlockedByPolicy: 2

##### -field PromptLargerThanContext: 3

##### -field PromptBlockedByPolicy: 4

##### -field ResponseBlockedByPolicy: 5

#### -remarks

#### -see-also

#### -examples


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelSkill
-api-type: winrt enum
--->

### LanguageModelSkill enumeration

```
public enum LanguageModelSkill
```


#### -description

#### -enum-fields

##### -field General: 0

##### -field TextToTable: 1

##### -field Summarize: 2

##### -field Rewrite: 3

#### -remarks

#### -see-also

#### -examples









## Related content

- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)
- [Get started with Phi Silica in the Windows App SDK](phi-silica.md)
- [Windows App SDK](/windows/apps/windows-app-sdk/)
- [Latest release notes for the Windows App SDK](/windows/apps/windows-app-sdk/release-channels)
