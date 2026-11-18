---
title: Chat completion with Windows ML Tasks
description: Use the experimental Windows ML Chat Completion Task to apply a model's chat template to messages and stream replies from a text generation session.
author: andrewleader
ms.author: aleader
ms.topic: concept-article
ms.date: 11/16/2026
dev_langs:
- cpp
- csharp
- python
---

# Chat completion with Windows ML Tasks

> [!IMPORTANT]
> The Chat Completion Task is experimental and **not supported** for use in production environments.

The Chat Completion Task composes a tokenizer that exposes a structured conversation formatter with a [text generation](../runtime/text-generation.md) session. You send a list of role-tagged messages. The Task applies the model's own chat template, starts generation, and streams the reply as content, reasoning, and tool-call events.

Your app owns the conversation. It appends each reply to its message list and sends the whole list on the next turn. The text generation session can come from any way of [choosing how a Task runs your model](./choose-model-path.md), and the chat session, events, and result work the same for each.

## Complete a conversation

The following examples assume that you already have a text generation Task, configuration, and tokenizer. The chat session wraps the text generation session, and the tokens that session holds carry over from one turn to the next.

### [C#](#tab/csharp)

Create a `ChatGenerator` from the text generation Task, configuration, and tokenizer. It creates the text generation session and the chat session over it, and keeps both on a thread of their own, because a session belongs to the thread that created it. It streams replies as `IAsyncEnumerable<T>`:

```csharp
// Temporary: ChatGenerator and the other wrapper types in the WinMLSamples.Runtime
// namespaces aren't in the Windows ML package yet. Copy the wrapper files from
// https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp
// into your project. A future update will include the C# APIs natively.
using WinMLSamples.Runtime;
using WinMLSamples.Runtime.Tasks;

// The chat session wraps the text generation session, and the tokens that
// session holds carry over from one turn to the next.
await using ChatGenerator chat = await ChatGenerator.CreateAsync(tasks, task, configuration, tokenizer);
```

Each turn sends the full history:

```csharp
// Send the conversation, stream the reply, and return it so that
// the caller can add it to the history.
static async Task<string> CompleteTurnAsync(
    ChatGenerator chat,
    TextGenerationOptions options,
    List<ConversationMessage> history,
    CancellationToken cancellationToken)
{
    ConversationRequest request = new()
    {
        FormatterMode = ConversationFormatterMode.ModelDefault,
        Messages = [.. history],
        AddGenerationPrompt = true,
    };

    // Content events carry the reply as the model generates it.
    await foreach (ChatCompletionUpdate update in chat.GenerateAsync(request, options, cancellationToken))
    {
        if (update.Type == ConversationOutputEventType.ContentDelta)
        {
            Console.Write(update.Text);
        }
        else if (update.Type == ConversationOutputEventType.Error)
        {
            throw WinMLException.GetExceptionForHResult(update.ErrorCode);
        }
    }

    // The outcome has the reply, the token counts, and how many prompt tokens
    // the session reused from the previous turn.
    ChatCompletionOutcome outcome = chat.LastOutcome!;
    Console.WriteLine(
        $"\n  Tokens: prompt={outcome.PromptTokenCount} " +
        $"(reused={outcome.PromptCacheUse.CachedPromptTokenCount}) " +
        $"completion={outcome.CompletionTokenCount}");

    return outcome.Content;
}
```

Call it once for each question, and add the reply to the history after each turn:

```csharp
string[] questions =
[
    "Name three primary colors.",
    "Which of those is the color of the sky?",
];

using TextGenerationOptions options = tasks.TextGeneration.CreateTextGenerationOptions(48);
List<ConversationMessage> history = [];
foreach (string question in questions)
{
    history.Add(new ConversationMessage { Role = "user", Content = question });
    Console.Write($"User: {question}\nAssistant: ");
    string reply = await CompleteTurnAsync(chat, options, history, CancellationToken.None);
    history.Add(new ConversationMessage { Role = "assistant", Content = reply });
}

// Closing the sessions reports a failure that disposing them would not.
await chat.CloseAsync();
```

Cancelling the `CancellationToken` stops generation in the Runtime.

### [C++](#tab/cpp)

Create the chat Task from the model's tokenizer, then a chat session over the text generation session. The session must come from the same Runtime and tokenizer as the Task, or `CreateSession` returns `E_INVALIDARG`:

```cpp
ComPtr<IWinMLChatCompletionTask> chatTask;
THROW_IF_FAILED(winml::tasks::CreateChatCompletionTask(
    textGeneration.tasks.Get(), tokenizer.Get(), chatTask.GetAddressOf()));
ComPtr<IWinMLChatCompletionSession> chat;
THROW_IF_FAILED(chatTask->CreateSession(textGeneration.session.Get(), chat.GetAddressOf()));

ComPtr<IWinMLCancellationSource> cancellation;
THROW_IF_FAILED(textGeneration.tasks->CreateCancellationSource(cancellation.GetAddressOf()));
```

Each turn sends the full history. `Generate` takes a complete `WINML_CONVERSATION_REQUEST` plus optional generation options and a cancellation source, and you read events from the returned pull stream before you read the terminal result. Messages point at their strings, so keep each reply alive for as long as you send the history:

```cpp
// Send the conversation, stream the reply, and return it so that
// the caller can add it to the history.
HRESULT CompleteTurn(
    IWinMLChatCompletionSession* chat,
    IWinMLTextGenerationOptions* options,
    IWinMLCancellationSource* cancellation,
    const std::vector<WINML_CONVERSATION_MESSAGE>& history,
    std::wstring& reply)
{
    WINML_CONVERSATION_REQUEST request = {};
    request.formatterMode = WINML_CONVERSATION_FORMATTER_MODE_MODEL_DEFAULT;
    request.messageCount = static_cast<UINT32>(history.size());
    request.messages = history.data();
    request.addGenerationPrompt = TRUE;

    ComPtr<IWinMLChatCompletionPullStream> stream;
    RETURN_IF_FAILED(chat->Generate(&request, options, cancellation, stream.GetAddressOf()));

    // Content events carry the reply as the model generates it.
    for (;;)
    {
        WINML_CHAT_COMPLETION_READ_STATUS status{};
        ComPtr<IWinMLConversationOutputEvent> event;
        RETURN_IF_FAILED(stream->ReadNext(&status, event.GetAddressOf()));
        if (status == WINML_CHAT_COMPLETION_READ_STATUS_COMPLETED)
        {
            break;
        }

        WINML_CONVERSATION_OUTPUT_EVENT_TYPE type{};
        RETURN_IF_FAILED(event->GetType(&type));
        if (type == WINML_CONVERSATION_OUTPUT_EVENT_TYPE_CONTENT_DELTA)
        {
            LPCWSTR text = nullptr;
            RETURN_IF_FAILED(event->GetText(&text));
            std::wprintf(L"%ls", text != nullptr ? text : L"");
        }
        else if (type == WINML_CONVERSATION_OUTPUT_EVENT_TYPE_ERROR)
        {
            HRESULT error = E_FAIL;
            LPCWSTR message = nullptr;
            RETURN_IF_FAILED(event->GetError(&error, &message));
            return FAILED(error) ? error : E_FAIL;
        }
    }

    // The result stays readable until the stream closes.
    ComPtr<IWinMLChatCompletionResult> result;
    RETURN_IF_FAILED(stream->GetResult(result.GetAddressOf()));
    HRESULT error = E_UNEXPECTED;
    RETURN_IF_FAILED(result->GetErrorCode(&error));
    RETURN_IF_FAILED(error);

    LPCWSTR content = nullptr;
    UINT32 promptTokenCount = 0;
    UINT32 completionTokenCount = 0;
    WINML_CHAT_COMPLETION_PROMPT_CACHE cacheUse{};
    RETURN_IF_FAILED(result->GetContent(&content));
    RETURN_IF_FAILED(result->GetTokenCounts(&promptTokenCount, &completionTokenCount));
    RETURN_IF_FAILED(result->GetPromptCacheUse(&cacheUse));
    reply = content != nullptr ? content : L"";

    std::wprintf(L"\n  Tokens: prompt=%u (reused=%u) completion=%u\n",
        promptTokenCount, cacheUse.cachedPromptTokenCount, completionTokenCount);

    return stream->Close();
}
```

Call it once for each question, and add the reply to the history after each turn:

```cpp
const LPCWSTR questions[] = {
    L"Name three primary colors.",
    L"Which of those is the color of the sky?",
};
std::wstring replies[std::size(questions)];
std::vector<WINML_CONVERSATION_MESSAGE> history;
for (size_t turn = 0; turn < std::size(questions); ++turn)
{
    history.push_back({L"user", questions[turn]});
    std::wprintf(L"User: %ls\nAssistant: ", questions[turn]);
    THROW_IF_FAILED(CompleteTurn(chat.Get(), textGeneration.options.Get(),
        cancellation.Get(), history, replies[turn]));
    history.push_back({L"assistant", replies[turn].c_str()});
}

THROW_IF_FAILED(chat->Close());
```

### [Python](#tab/python)

```python
from windowsml.runtime import ChatRole
from windowsml.tasks import (
    ChatCompletionEventType,
    ChatCompletionMessage,
    ChatCompletionRequest,
    TextGenerationOptions,
)

# The chat Task runs on a text generation session built with the same tokenizer.
chat = tasks.create_chat_completion_task(
    tokenizer, default_text_generation_session=text_session
)
options = TextGenerationOptions(max_new_tokens=48)

history = []
for question in ("Name three primary colors.", "Which of those is the color of the sky?"):
    history.append(ChatCompletionMessage(role=ChatRole.USER, content=question))
    with chat.stream(ChatCompletionRequest(messages=tuple(history)), options) as stream:
        for event in stream:
            event.raise_for_error()
            if event.type == ChatCompletionEventType.CONTENT_DELTA:
                print(event.text, end="", flush=True)
        print()

        # The result stays readable until the stream closes.
        result = stream.result
        result.raise_for_error()
        reused = result.prompt_cache_use.cached_prompt_token_count
    print(f"Tokens: prompt={result.prompt_token_count} (reused={reused}) "
          f"completion={result.completion_token_count}")
    history.append(ChatCompletionMessage(role=ChatRole.ASSISTANT, content=result.content))
```

---

## Reuse the conversation

When the formatted prompt starts with the token sequence that the session retained, generation continues with the new suffix instead of evaluating the whole prompt again. The result content is the same either way. Only the amount of prompt work differs. The result reports the full prompt count, the retained-prefix count, and the tokens evaluated, so you can see the reuse:

```text
User: Name three primary colors.
Assistant: Three primary colors are red, blue, and yellow.
  Tokens: prompt=34 (reused=0) completion=11
User: Which of those is the color of the sky?
Assistant: The color of the sky is blue.
  Tokens: prompt=65 (reused=45) completion=8
```

## Results and cancellation

The terminal result reports the final content, reasoning, tool calls, prompt and completion token counts, the finish reason, and the underlying text generation result. Pass an `IWinMLCancellationSource` to `Generate` and call `Cancel` from any thread to stop generation.

Sessions and streams belong to the thread that created them. Calls from another thread return `RPC_E_WRONG_THREAD`.

## Image input

A chat session can pass images to a model that supports them. Set a media encoder with `SetMediaEncoder`, then call `GenerateWithMedia` with bindings that match each image content part to a tensor.

## Next steps

- [Windows ML Task APIs overview](./overview.md)
- [Generate text with your own language model](../runtime/text-generation.md)
- [Windows ML samples](../samples.md)
