---
title: API ref for Phi Silica APIs in the Windows App SDK
description: Learn about the new Artificial Intelligence (AI) text recognition APIs shipping with Windows App SDK 1.6 Experimental that can be used connect your Windows app to on-device language models, including Phi Silica, our most powerful NPU-tuned local language model yet.
ms.topic: article
ms.date: 05/15/2024
ms.author: kbridge
author: karl-bridge-microsoft
---

# API ref for Phi Silica APIs in the Windows App SDK

Learn about the new Artificial Intelligence (AI) Phi Silica APIs shipping with Windows App SDK 1.6 Experimental that can be used to access on-device language models (including Phi Silica, our most powerful NPU-tuned local language model yet) and enable local processing and generation of chat, math solving, code generation, reasoning over text, and more.

For more details, see [Introducing Phi Silica in the Windows App SDK](phi-silica.md).

---

<!---
-api-id: N:Microsoft.Windows.AI.Generative
-api-type: winrt namespace
--->

## Microsoft.Windows.AI.Generative namespace

Provides APIs for local, on-device generative AI prompt processing and responses.

### Remarks

### See also

### Examples


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModel
-api-type: winrt class
--->

### Microsoft.Windows.AI.Generative.LanguageModel class

<!--
public sealed class LanguageModel : System.IDisposable
-->

Represents an object that can interact with a local language model to generate responses for a provided prompt.

#### Remarks

#### See also

#### Examples



<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.Close
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.Close method

<!--
// This member is not implemented in C#
-->

Disposes of the object and associated resources.

##### Remarks

Not implemented in C#.

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.CreateAsync
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.CreateAsync method

<!--
public static Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModel> CreateAsync ();
-->

Asynchronously creates a new instance of the LanguageModel class.

##### Returns

A new instance of the TextRecognizer class.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(System.String)
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseAsync(System.String) method

<!--
public Windows.Foundation.IAsyncOperation<Microsoft.Windows.AI.Generative.LanguageModelResponse> GenerateResponseAsync (string prompt);
-->

Generates and returns a complete response for a single prompt.

##### Parameters

###### prompt

A prompt in the form of a question.

##### Returns

A response string and status.

##### Exceptions

**ArgumentException**: The specified prompt is longer than the maximum number of tokens the model can accept.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(System.String)
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.GenerateResponseWithProgressAsync(System.String) method

<!--
public Windows.Foundation.IAsyncOperationWithProgress<Microsoft.Windows.AI.Generative.LanguageModelResponse,string> GenerateResponseWithProgressAsync (string prompt);
-->

Generates and streams a response through a progress handler. Partial results can be retrieved while generation is in progress.

##### Parameters

###### prompt

The prompt for the response.

##### Returns

A response string and status.

The next token of the string that is being added to the full response as the model returns it, this is the delta from the previous LanguageModelReponse set as the result OnProgress.

##### Exceptions

**ArgumentException**: The specified prompt is longer than the maximum number of tokens the model can accept.

##### Remarks

OnProgress events occur on generation of each single word in the response.

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.IsAvailable
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.IsAvailable method

<!--
public static bool IsAvailable ();
-->

Retrieves whether the required AI Model is available.

##### Returns

True, if required AI Model is available. Otherwise, false.

##### Remarks

##### See also

##### Examples


<!---
-api-id: M:Microsoft.Windows.AI.Generative.LanguageModel.MakeAvailableAsync
-api-type: winrt method
--->

#### Microsoft.Windows.AI.Generative.LanguageModel.MakeAvailableAsync method

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
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponse
-api-type: winrt class
--->

### Microsoft.Windows.AI.Generative.LanguageModelResponse class

<!--
public sealed class LanguageModelResponse
-->

Represents a response string and status.

#### Remarks

#### See also

#### Examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponse.Response
-api-type: winrt property
--->

#### Microsoft.Windows.AI.Generative.LanguageModelResponse.Response property

<!--
public string Response { get; }
-->

Gets the response string returned by the language model based on the provided prompt.

##### Property value

The response string returned by the language model based on the provided prompt.

##### Remarks

##### See also

##### Examples


<!---
-api-id: P:Microsoft.Windows.AI.Generative.LanguageModelResponse.Status
-api-type: winrt property
--->

#### Microsoft.Windows.AI.Generative.LanguageModelResponse.Status property

<!--
public Microsoft.Windows.AI.Generative.LanguageModelResponseStatus Status { get; }
-->

Gets the response status based on the provided prompt.

##### Property value

The response string returned by the language model based on the provided prompt.

##### Remarks

Any value other than `Succeeded` or `InProgress` is considered a failure.

##### See also

##### Examples


<!---
-api-id: T:Microsoft.Windows.AI.Generative.LanguageModelResponseStatus
-api-type: winrt enum
--->

### Microsoft.Windows.AI.Generative.LanguageModelResponseStatus enum

<!--
public enum LanguageModelResponseStatus
-->

Specifies the possible response status values for the provided prompt.

#### Enum fields

##### Complete: 0

Response is complete.

##### InProgress: 1

Response is in progress.

##### BlockedByPolicy: 2

Response is blocked by a policy setting.

#### Remarks

#### See also

#### Examples


