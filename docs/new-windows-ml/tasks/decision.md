---
title: Make decisions with Windows ML Tasks
description: Use the experimental Windows ML Decision Task to get a probability for each option of a typed question about a text, without generating text.
author: andrewleader
ms.author: aleader
ms.topic: concept-article
ms.date: 11/18/2026
dev_langs:
- cpp
- csharp
- python
---

# Make decisions with Windows ML Tasks

> [!IMPORTANT]
> The Decision Task is experimental, **not supported** for use in production environments, and requires Windows ML 2.9 experimental or later.

The Decision Task evaluates a state, such as a message, a support ticket, or a serialized document, against typed questions. For each question it returns a probability for every option, the selected option, a confidence, and a recommendation to act or to escalate. A decision is a single evaluation per question, so the Task generates no text.

The Decision Task takes a published decision model folder as its model, and Windows ML compiles the model for the GPU with the [CGC backend](../runtime/model-compilation-execution.md). For now, use Laya, the validated decision model in the [recommended models](../runtime/model-compilation-execution.md#recommended-models). It requires a GPU execution target, and a CPU target returns `HRESULT_FROM_WIN32(ERROR_NOT_SUPPORTED)`. For how Tasks run models, see [Choose how a Task runs your model](./choose-model-path.md).

## Question kinds

| Kind | Options |
|---|---|
| Choice | At least one. Each has a unique label and an optional description. |
| Score | At least one. Option `i` is level `i`, and its description states the criterion for that level. |
| Yes/no | None, which uses the model's default description of both answers, or exactly two (false, then true). |

A question can set an act threshold from 0 to 1. The answer recommends `ACT` when its confidence reaches the threshold and `ESCALATE` otherwise. Without a threshold, the recommendation is `ACT` when the model's act probability is at least 0.5.

## Decide

Load the model folder with `LoadModelFromPath`, create the Task with `CreateDecisionTaskFromModel`, create a session, and call `Decide` with a state and its questions. The tokenizer is optional: with `nullptr`, the Task creates the model's own tokenizer from the model folder.

### [C#](#tab/csharp)

```csharp
// Temporary: WinMLRuntime and the other wrapper types in the WinMLSamples.Runtime
// namespaces aren't in the Windows ML package yet. Copy the wrapper files from
// https://github.com/microsoft/WindowsML/tree/main/Samples/Runtime/shared/csharp
// into your project. A future update will include the C# APIs natively.
using WinMLSamples.Runtime;
using WinMLSamples.Runtime.Tasks;

// Load the decision model and create the task on a GPU target.
using WinMLRuntime runtime = WinMLRuntime.Create();
using ExecutionTarget target = runtime.CreateExecutionTarget(
    ExecutionTargetKind.Gpu, ExecutionTargetPreference.Performance);
using Model model = runtime.LoadModelFromPath(modelFolder);
using WinMLTasks tasks = WinMLTasks.Create(runtime);
using DecisionTask task = tasks.Decision.CreateDecisionTaskFromModel(target, model);

// Describe a choice question and evaluate a state against it.
DecisionOption[] options =
[
    new("billing", "A question about an invoice or payment"),
    new("technical", "A problem using the product"),
];
DecisionQuestion question = new()
{
    Kind = DecisionQuestionKind.Choice,
    Instructions = "Which team should handle this support ticket?",
    Options = options,
};
DecisionRequest request = new()
{
    State = "I was charged twice for my subscription.",
    Questions = [question],
};

// A Decider keeps the session on a thread of its own.
await using Decider decider = await Decider.CreateAsync(tasks, task);
using DecisionResult result = await decider.DecideAsync(request, CancellationToken.None);

// Closing the session reports a failure that disposing it would not.
await decider.CloseAsync();

DecisionAnswer answer = result.GetAnswer(0);
float[] probabilities = new float[options.Length];
result.GetProbabilities(0, probabilities);
```

### [C++](#tab/cpp)

```cpp
// Load the decision model and create the task on a GPU target.
wil::com_ptr<IWinMLModel> model;
RETURN_IF_FAILED(runtime->LoadModelFromPath(modelFolder, nullptr, model.put()));
wil::com_ptr<IWinMLDecisionTaskFactory> factory;
RETURN_IF_FAILED(tasks->QueryInterface(IID_PPV_ARGS(factory.put())));
wil::com_ptr<IWinMLDecisionTask> task;
RETURN_IF_FAILED(factory->CreateDecisionTaskFromModel(
    gpuTarget.get(), nullptr, model.get(), nullptr, task.put()));

wil::com_ptr<IWinMLDecisionSession> session;
RETURN_IF_FAILED(task->CreateSession(session.put()));

// Describe a choice question and evaluate a state against it.
const WINML_DECISION_OPTION options[] = {
    {L"billing", L"A question about an invoice or payment"},
    {L"technical", L"A problem using the product"},
};
WINML_DECISION_QUESTION question{};
question.kind = WINML_DECISION_QUESTION_KIND_CHOICE;
question.instructions = L"Which team should handle this support ticket?";
question.optionCount = ARRAYSIZE(options);
question.options = options;
const WINML_DECISION_REQUEST request{L"I was charged twice for my subscription.", 1, &question};

wil::com_ptr<IWinMLDecisionResult> result;
RETURN_IF_FAILED(session->Decide(&request, nullptr, result.put()));
WINML_DECISION_ANSWER answer{};
RETURN_IF_FAILED(result->GetAnswer(0, &answer));
float probabilities[ARRAYSIZE(options)]{};
RETURN_IF_FAILED(result->GetProbabilities(0, ARRAYSIZE(probabilities), probabilities));
RETURN_IF_FAILED(session->Close());
```

### [Python](#tab/python)

Call `tasks.create_decision_task(target, model)` and `session.decide(state, questions)` with `DecisionQuestion` and `DecisionOption` values.

---

## Read the answer

| Field | Meaning |
|---|---|
| `selectedOption` | The most probable option. For yes/no questions, 1 means true. |
| `confidence` | The probability of the selected option. |
| `distributionConfidence` | One minus the normalized entropy of the distribution: 1 when all probability is on one option, 0 when uniform. It isn't a calibrated probability. |
| `expectedScore` | For score questions, the expected level. |
| `actProbability` | The model's estimate that the answer can be acted on. |
| `recommendation` | `ACT` or `ESCALATE`. |

A state that's longer than the room a question leaves in the model's sequence is truncated at its end. Pass a structured state in its serialized text form.

## Keep compiled programs

As with the other Tasks that use the CGC backend, the first session compiles programs for the GPU. Set `compiledProgramDirectory` in the model options to reuse them in later processes. See [Cache compiled programs](./choose-model-path.md#cache-compiled-programs).

## Next steps

- [Windows ML Task APIs overview](./overview.md)
- [Choose how a Task runs your model](./choose-model-path.md)
