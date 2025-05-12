---
title: Fine-Tune the Phi Silica model using LoRA
description: Learn about t
ms.topic: article
ms.date: 05/12/2025
ms.author: mattwoj
author: mattwojo
dev_langs:
- csharp
- cpp
---

# LoRA Fine-Tuning for Phi Silica

Low Rank Adaptation (LoRA) can be utilized to fine-tune the [Phi Silica model](phi-silica.md) to enhance its performance for your specific use-case. By using LoRA to optimize Phi Silica, Microsoft Windows local language model, you can achieve more accurate results. This process involves training a LoRA adapter and then applying it during inference to improve the model's accuracy.

## Prerequisites

- You have identified a use case for enhancing the response of Phi Silica.
- You have chosen an evaluation criteria to decide what a ‘good response’ is.
- You have tried the Phi Silica prompt API and it does not meet your evaluation criteria.

## Train your adapter

<TO-DO> Content needed.

## Generate responses

<TO-DO> Content needed.

### [C#](#tab/csharp0)

```csharp
using Microsoft.Windows.AI.Text;
using Microsoft.Windows.AI.Text.Experimental;

// Path to the LoRA adapter file
string adapterFilePath = "C:/path/to/adapter/file.safetensors";

// Prompt to be sent to the LanguageModel
string prompt = "How do I add a new project to my Visual Studio solution?";

// Wait for LanguageModel to be ready
if (LanguageModel.GetReadyState() == AIFeatureReadyState.NotReady)
{
    var languageModelDeploymentOperation = LanguageModel.EnsureReadyAsync();
    await languageModelDeploymentOperation;
 }  
// Create the LanguageModel session
var session = LanguageModel.CreateAsync();
// Create the LanguageModelExperimental
var languageModelExperimental = new LanguageModelExperimental(session);
  
// Load the LoRA adapter
 LowRankAdaptation loraAdapter = languageModelExperimental.LoadAdapter(adapterFilePath);
  
// Set the adapter in LanguageModelOptionsExperimental
LanguageModelOptionsExperimental options = new LanguageModelOptionsExperimental
{
LoraAdapter = loraAdapter
 };
  
 // Generate a response with the LoRA adapter provided in the options
 var response = await languageModelExperimental.GenerateResponseAsync(prompt, options);
```

### [C++](#tab/cpp0)
```cpp
<TO DO> Content needed
```

---

## LoRA in the AI Toolkit Playground

<TO DO> Content needed

## LoRA in the AI Dev Gallery

<TO DO> Content needed

## Responsible AI - Risks and limitations of fine-tuning

When customers fine-tune Phi Silica, it can improve model performance and accuracy on specific tasks and domains, but it can also introduce new risks and limitations that customers should be aware of. Some of these risks and limitations are:

- Data quality and representation: The quality and representativeness of the data used for fine-tuning can affect the model's behavior and outputs. If the data is noisy, incomplete, outdated, or if it contains harmful content like stereotypes, the model can inherit these issues and produce inaccurate or harmful results. For example, if the data contains gender stereotypes, the model can amplify them and generate sexist language. Customers should carefully select and pre-process their data to ensure that it is relevant, diverse, and balanced for the intended task and domain.

- Model robustness and generalization: The model's ability to handle diverse and complex inputs and scenarios can decrease after fine-tuning, especially if the data is too narrow or specific. The model can overfit to the data and lose some of its general knowledge and capabilities. For example, if the data is only about sports, the model can struggle to answer questions or generate text about other topics. Customers should evaluate the model's performance and robustness on a variety of inputs and scenarios and avoid using the model for tasks or domains that are outside its scope.

- Regurgitation: While your training data is not available to Microsoft or any third-party customers, poorly fine-tuned models may regurgitate, or directly repeat, training data. Customers are responsible for removing any PII or otherwise protected information from their training data and should assess their fine-tuned models for over-fitting or otherwise low-quality responses. To avoid regurgitation, customers are encouraged to provide large and diverse datasets.

- Model transparency and explainability: The model's logic and reasoning can become more opaque and difficult to understand after fine-tuning, especially if the data is complex or abstract. A fine-tuned model can produce outputs that are unexpected, inconsistent, or contradictory, and customers may not be able to explain how or why the model arrived at those outputs. For example, if the data is about legal or medical terms, the model can generate outputs that are inaccurate or misleading, and customers may not be able to verify or justify them. Customers should monitor and audit the model's outputs and behavior and provide clear and accurate information and guidance to the end-users of the model.

## See also

- [Phi Silica](./phi-silica.md)