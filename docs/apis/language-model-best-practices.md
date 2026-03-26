---
title: Language model best practices
description: 
ms.topic: overview
ms.date: 03/26/2026
---

# Language model best practices

This topic provides developer guidance and describes various best practices for the [LanguageModel](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) APIs supported by [Phi Silica](phi-silica.md). It covers both the API functionality and the develpoer requirements for incorporating the supported features into a Windows app.

## Handling non-deterministic output

Most code behaves predictably — the same input always produces the same output. The [LanguageModel](/windows/windows-app-sdk/api/winrt/microsoft.windows.ai.text.languagemodel) APIs don't work that way, as the exact same prompt can yield a different response each time it's submitted due to a randomizing factor built into the APIs.

The Phi Silica model is sensitive to any randomness, with small changes to the input and options producing large changes in the output. For example, the introduction of a single space or typo in a prompt might turn a 100 token answer into a 1000 token answer.

### Why outputs vary

The default sampling parameters introduce randomness into token selection:

| Parameter | Default | Effect on variability |
| --- | --- | --- |
| Temperature | 0.9 | Higher values increase randomness; lower values produce more focused output. |
| TopP | 0.9 | Controls cumulative probability threshold for token candidates. |
| TopK | 40 | Limits how many tokens are considered at each step; lower values reduce variability. |

### Guidance

- **Do not write logic that depends on exact output matching.** The same prompt can produce different text on every call.
- Lowering `Temperature` and `TopK` reduces variability but does not guarantee determinism. There is no exposed seed parameter.
- Setting `Temperature = 0` is not guaranteed to produce identical outputs across calls.

### Reducing variability

```c#
using Microsoft.Windows.AI.Text;

async Task<string> GenerateWithLowVariability(LanguageModel languageModel, string prompt)
{
    var options = new LanguageModelOptions();
    options.Temperature = 0.1f;
    options.TopK = 1;

    var result = await languageModel.GenerateResponseAsync(prompt, options);
    return result.Text;
}
```

### Anti-pattern: fragile string comparison

```c#
// DO NOT do this - output is non-deterministic
var result = await languageModel.GenerateResponseAsync("Is 2 > 1? Answer yes or no.");
if (result.Text == "Yes")  // Fragile: response may be "yes", "Yes.", "Yes, 2 is greater", etc.
{
    // ...
}
```

Instead, parse or classify the response in a way that tolerates variation (e.g., check whether
the response contains "yes" case-insensitively, or use the model for structured extraction).

### Semantic comparison with embeddings

Rather than comparing response text directly, use `GenerateEmbeddingVectors` and cosine
similarity to determine whether two outputs are semantically equivalent. This approach is
resilient to differences in wording, punctuation, and formatting.

```c#
using Microsoft.Windows.AI.Text;

double CosineSimilarity(float[] a, float[] b)
{
    double dot = 0, magA = 0, magB = 0;
    for (int i = 0; i < a.Length; i++)
    {
        dot += a[i] * b[i];
        magA += a[i] * a[i];
        magB += b[i] * b[i];
    }
    return dot / (Math.Sqrt(magA) * Math.Sqrt(magB));
}

async Task<bool> AreResponsesSemanticallyEqual(
    LanguageModel languageModel, string prompt, double threshold = 0.9)
{
    var result1 = await languageModel.GenerateResponseAsync(prompt);
    var result2 = await languageModel.GenerateResponseAsync(prompt);

    var embedding1 = languageModel.GenerateEmbeddingVectors(result1.Text);
    var embedding2 = languageModel.GenerateEmbeddingVectors(result2.Text);

    // Extract the float arrays from the first embedding vector in each result
    float[] values1 = new float[embedding1.EmbeddingVectors[0].Size];
    embedding1.EmbeddingVectors[0].GetValues(ref values1);

    float[] values2 = new float[embedding2.EmbeddingVectors[0].Size];
    embedding2.EmbeddingVectors[0].GetValues(ref values2);

    double similarity = CosineSimilarity(values1, values2);
    return similarity >= threshold;
}
```

## Using Context for Multi-Turn Conversations

Each call to `GenerateResponseAsync` without a `LanguageModelContext` is stateless. The model has
no memory of prior prompts or responses. To build a multi-turn conversation, you must create and
pass a `LanguageModelContext`.

### How context works

- `CreateContext()` or `CreateContext(systemPrompt)` returns a `LanguageModelContext` that
  accumulates conversation history.
- When you pass a context to `GenerateResponseAsync`, the call modifies the context in-place,
  appending both the prompt and the response to the conversation history.
- The system prompt, set at context creation time, guides model behavior for the entire
  conversation.

### Guidance

- Create a context with a system prompt to set the model's role and behavioral boundaries.
- Pass the same context to every `GenerateResponseAsync` call within a conversation.
- `LanguageModelContext` implements `IClosable` — dispose it when the conversation ends.
- If content moderation blocks a prompt or response, the context state is unspecified. Consider
  creating a new context after a moderation block.

### Proper multi-turn conversation

```c#
using Microsoft.Windows.AI.Text;

async Task RunConversation(LanguageModel languageModel)
{
    using var context = languageModel.CreateContext(
        "You are a helpful cooking assistant. Answer questions about recipes and techniques.");

    var options = new LanguageModelOptions();

    var result1 = await languageModel.GenerateResponseAsync(
        context, "How do I make a roux?", options);
    Console.WriteLine(result1.Text);

    // Context now contains the first exchange — the model remembers it
    var result2 = await languageModel.GenerateResponseAsync(
        context, "What ratio of butter to flour should I use?", options);
    Console.WriteLine(result2.Text);

    // The model can reference both prior turns
    var result3 = await languageModel.GenerateResponseAsync(
        context, "Can I use olive oil instead?", options);
    Console.WriteLine(result3.Text);
}
```

### Anti-pattern: stateless calls losing conversational coherence

```c#
// DO NOT do this for multi-turn conversations
var result1 = await languageModel.GenerateResponseAsync("How do I make a roux?");
// No context passed — next call has no memory of the first
var result2 = await languageModel.GenerateResponseAsync("What ratio should I use?");
// The model does not know "ratio" refers to the roux ingredients
```

## Managing Context Length

The model has a finite context window. The API does not automatically truncate or summarize
conversation history. Context length management is the developer's responsibility.

Context is consumed by the system prompt, accumulated conversation history (all prior prompts
and responses), and the current prompt. As conversations grow, the remaining space for new
prompts shrinks.

### Key API: `GetUsablePromptLength`

`GetUsablePromptLength(context, prompt)` returns a character index into the prompt string
indicating where the context window ran out of space. If the return value equals the prompt
length, the entire prompt fits.

### Strategies

1. **Check before sending** — call `GetUsablePromptLength` before each `GenerateResponseAsync`.
2. **Trim the prompt** — if the return value is less than the prompt length, truncate or
   rephrase the prompt to fit.
3. **Reset context** — when history fills up, create a new context. Optionally carry forward a
   summary of the conversation as the system prompt for the new context.
4. **Handle `PromptLargerThanContext`** — always check `result.Status` and handle this status
   gracefully.

### Pre-send length check with trimming

```c#
using Microsoft.Windows.AI.Text;

async Task<LanguageModelResponseResult> SendWithLengthCheck(
    LanguageModel languageModel,
    LanguageModelContext context,
    string prompt,
    LanguageModelOptions options)
{
    ulong usableLength = languageModel.GetUsablePromptLength(context, prompt);

    if (usableLength < (ulong)prompt.Length)
    {
        // Trim prompt to fit the remaining context window
        prompt = prompt.Substring(0, (int)usableLength);
    }

    return await languageModel.GenerateResponseAsync(context, prompt, options);
}
```

### Context reset when the window fills up

```c#
using Microsoft.Windows.AI.Text;

async Task<LanguageModelResponseResult> SendWithContextReset(
    LanguageModel languageModel,
    ref LanguageModelContext context,
    string prompt,
    LanguageModelOptions options,
    string baseSystemPrompt)
{
    ulong usableLength = languageModel.GetUsablePromptLength(context, prompt);

    if (usableLength == 0)
    {
        // Context is full — summarize and start fresh
        var summaryResult = await languageModel.GenerateResponseAsync(
            "Summarize our conversation so far in 2-3 sentences.");

        context.Dispose();
        context = languageModel.CreateContext(
            baseSystemPrompt + "\n\nPrior conversation summary: " + summaryResult.Text);
    }

    return await languageModel.GenerateResponseAsync(context, prompt, options);
}
```

## Handling Response Status

Always check `result.Status` before using `result.Text`. A non-`Complete` status means the
text may be empty or incomplete.

| Status | Meaning | Recommended handling |
|-|-|-|
| `Complete` | Full response generated successfully | Use `result.Text` |
| `InProgress` | Generation is still running | Wait for completion via the async operation |
| `BlockedByPolicy` | Generative AI blocked by system policy | Inform the user that the feature is unavailable |
| `PromptLargerThanContext` | Prompt exceeds the context window | Trim the prompt or reset the context |
| `PromptBlockedByContentModeration` | Input blocked by content moderation | Inform the user their input was filtered |
| `ResponseBlockedByContentModeration` | Output blocked by content moderation | Inform the user the response was filtered; consider rephrasing |
| `Error` | An error occurred | Check `result.ExtendedError` for details |

```c#
using Microsoft.Windows.AI.Text;

void HandleResponse(LanguageModelResponseResult result)
{
    switch (result.Status)
    {
        case LanguageModelResponseStatus.Complete:
            Console.WriteLine(result.Text);
            break;

        case LanguageModelResponseStatus.BlockedByPolicy:
            Console.WriteLine("This feature is not available on this device.");
            break;

        case LanguageModelResponseStatus.PromptLargerThanContext:
            Console.WriteLine("Prompt is too long. Please shorten your input.");
            break;

        case LanguageModelResponseStatus.PromptBlockedByContentModeration:
            Console.WriteLine("Your input was blocked by content filtering.");
            break;

        case LanguageModelResponseStatus.ResponseBlockedByContentModeration:
            Console.WriteLine("The response was blocked by content filtering.");
            break;

        case LanguageModelResponseStatus.Error:
            Console.WriteLine($"Error: {result.ExtendedError}");
            break;
    }
}
```

## Resource Lifecycle Management

Both `LanguageModel` and `LanguageModelContext` implement `IClosable`. Failing to dispose them
can leak native resources.

### Guidance

- Use `using` statements or call `Dispose()` explicitly.
- Create one `LanguageModel` instance and reuse it across calls. Do not create a new instance
  for each request.
- Dispose `LanguageModelContext` when its conversation ends, not after every call.

```c#
using Microsoft.Windows.AI.Text;

async Task Example()
{
    // One LanguageModel instance, reused across conversations
    using LanguageModel languageModel = await LanguageModel.CreateAsync();

    // First conversation
    using (var context1 = languageModel.CreateContext("You are a math tutor."))
    {
        var options = new LanguageModelOptions();
        await languageModel.GenerateResponseAsync(context1, "What is 12 * 15?", options);
        await languageModel.GenerateResponseAsync(context1, "Now divide that by 3.", options);
    } // context1 disposed here

    // Second conversation — reuses the same LanguageModel
    using (var context2 = languageModel.CreateContext("You are a writing assistant."))
    {
        var options = new LanguageModelOptions();
        await languageModel.GenerateResponseAsync(context2, "Help me write an intro paragraph.", options);
    } // context2 disposed here
} // languageModel disposed here
```
