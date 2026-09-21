---
title: Local AI tutorial - Verify the feature
description: Verify a local AI feature in your WinUI 3 app by checking summary quality, cancellation, offline inference, and recoverable errors.
author: GrantMeStrength
ms.author: jken
ms.date: 09/21/2026
ms.topic: tutorial
---

# Verify the local AI feature

A successful build doesn't tell you whether a summary is accurate, whether cancellation works, or whether the app can recover from a download failure. In this final step, exercise the feature in the running app.

## Generate and inspect a summary

1. Run LocalNotes and select **Download and prepare model**. On the first run, allow time for the download and model loading to finish.
1. Enter this synthetic note:

    ```text
    The library workshop is on Friday at 2 PM in Room 4.
    Attendees should bring a laptop. Registration closes on Wednesday.
    The workshop is free. The organizer will email the slides afterward.
    ```

1. Select **Summarize**. Confirm that the app displays progress and eventually shows a nonempty summary without changing the note.
1. Compare the summary with the source. Check the day, time, room, registration deadline, and cost. The model might omit details, but it must not invent a different date or a registration fee.
1. Replace the note with unrelated content and summarize again. Confirm that facts from the first note don't leak into the second result.

Don't compare the output with a single expected string. Generation can vary between runs, model versions, and hardware. Evaluate whether the result is useful and faithful to the source, not whether it matches a screenshot word for word.

The screenshot in the introduction is from a real run, and it illustrates this distinction. Its source says that Alex **will order** kits and Priya **will post** an announcement, but the generated draft says **ordered** and **posted**. It also exceeds the requested three bullets. The code successfully ran local inference, but a person must correct the draft before using it.

> [!IMPORTANT]
> The result is an AI-generated draft, not a verified record. Local execution doesn't prevent hallucinations or prompt injection. Don't execute generated text as code, treat it as authorization, or automatically use it for consequential decisions.

## Check cancellation and input limits

| Action | Expected behavior |
|---|---|
| Clear the note or enter only spaces. | **Summarize** is disabled. |
| Enter a note at the 4,000-character input limit. | The input remains bounded; the UI doesn't submit an unbounded document. |
| Select **Summarize** repeatedly. | Only one generation runs at a time. |
| Select **Cancel** while generating. | The request stops, the original note remains, and the UI doesn't report a completed summary. |
| Summarize again after cancellation. | A new request completes without restarting the app. |
| Edit the source after generating a summary. | The app doesn't present the old summary as the result for the new text. |
| Close the window while generating. | The app cancels active work and releases its model resources. |

The input limit is a sample policy, not a model's context-window size. Characters and tokens aren't equivalent. For longer documents, design a separate chunking and summarization strategy and test it with the languages your app supports.

## Check local inference and setup failures

Model acquisition and inference are different operations. Verify them separately:

1. Prepare the model while connected to the internet.
1. Keep the app open and, when it won't interrupt other work, disconnect the network.
1. Summarize another note. A prepared, loaded model should generate the result locally.
1. Reconnect the network before testing model acquisition again.

This checks **warm offline inference**. It doesn't prove that a fresh install, a new model selection, or a cold app launch can complete setup offline. Test those scenarios separately before advertising full offline startup support.

On a separate test account or a disposable test device with no cached model, try preparing the model while offline. Confirm that the app reports the failure and lets you retry after reconnecting. Don't delete shared model caches or change firewall rules on a machine other people use.

Also test with insufficient disk space and unavailable model variants in a controlled environment. Keep the text editor usable when AI isn't available. Don't silently replace a local request with a cloud request.

## Check the Windows app experience

Use <kbd>Tab</kbd>, <kbd>Shift</kbd>+<kbd>Tab</kbd>, and <kbd>Enter</kbd> to reach and activate the commands. Check that a screen reader can identify the note, summary, and status. Resize the window and inspect light, dark, and contrast themes, along with enlarged text.

For background, see [Accessibility testing](/windows/apps/design/accessibility/accessibility-testing) and [Text scaling](/windows/apps/develop/input/text-scaling).

## Troubleshoot

| Symptom | What to check |
|---|---|
| Native library fails to load. | Build for the actual architecture, **ARM64** or **x64**, not **Any CPU**. Confirm that the Windows and .NET prerequisites match the project. |
| App doesn't launch with package identity. | Keep `Package.appxmanifest`, enable Developer Mode, and use the packaged launch profile rather than running the executable directly. |
| First setup takes longer than generation. | Check model and runtime-component download progress. Setup can be substantial even for a small model. |
| Model download fails. | Check connectivity, proxy policy, disk space, and the diagnostic message before retrying. |
| Model isn't found or can't load. | Check the current catalog, available model variants, driver requirements, and memory. Don't assume every catalog model works on every PC. |
| Summary is incomplete or inaccurate. | Compare with the source, shorten the note, and evaluate a more capable model. A token limit bounds output length; it doesn't guarantee accuracy. |
| Cancel doesn't stop immediately. | Cancellation is cooperative. Wait for native inference to return before starting another request or unloading the model. |

For SDK-specific troubleshooting, use the [Foundry Local documentation](/azure/foundry-local/get-started#troubleshooting).

## Validation for the tutorial code

The tutorial code was built for **ARM64 Debug** with the .NET 10 SDK and run with `winapp` on Windows build 26340. The running UI downloaded and loaded the CPU model, generated a real summary, canceled generation, rejected stale output after edits, and successfully generated again. The build completed without warnings or errors.

This run doesn't validate x64 execution, offline operation, accelerated inference, theme switching, fault injection, or signed release distribution. Use the checks above on the devices and configurations you intend to support.

## Apply the pattern to your app

You now have an AI feature with a defined input, a user-initiated local operation, and a reviewable output. Reuse the summarization service behind your app's own command and status UI instead of rebuilding your app around a chat experience.

Before distributing the feature, evaluate model licenses, download consent, storage cleanup, memory use, latency, accessibility, and output quality on your supported hardware. Add application-specific tests for misleading text and instructions embedded in documents. Model instructions aren't a security boundary.

Continue with the [Foundry Local SDK reference](/azure/foundry-local/reference/reference-sdk-current) for model lifecycle APIs and [Choose your Windows AI solution](../../../windows-ai-comparison.md) for other local AI options.
