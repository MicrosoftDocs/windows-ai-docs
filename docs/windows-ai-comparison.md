---
title: Choose your Windows AI solution
description: Scenario-based guidance for choosing between Windows AI APIs, Foundry Local, Windows ML, and Microsoft Foundry — including a terminology reference to decode the Windows AI landscape.
ms.date: 08/05/2026
ms.topic: article
keywords: windows ai, phi silica, foundry local, windows ml, copilot plus, windows ai apis, which api, choose
no-loc: [Microsoft Foundry on Windows, Windows AI APIs, Foundry Local, Windows ML, Phi Silica, Copilot+]
---

# Choose your Windows AI solution

The Windows AI landscape has grown quickly and the terminology can be hard to navigate. This page cuts through it: find your scenario, pick your starting point.

## Options

- **Ready-to-use AI models and APIs** — Models distributed and managed by Microsoft; minimal code, no ML expertise needed.
  - **[Windows AI APIs](./apis/get-started.md)** — LLM (Phi Silica), imaging models, OCR, semantic search, and more. Most APIs require a Copilot+ PC; some (Phi Silica on GPU, Speech Recognition, Video Super Resolution) also run on other Windows 11 hardware—see the [supported hardware table](./apis/index.md#supported-hardware).
  - **[Foundry Local](./foundry-local/get-started.md)** — 20+ open-source LLMs and speech models via an OpenAI-compatible API. Any Windows hardware.
- **Run any model locally** — Bring your own ONNX model with full control over the inference pipeline.
  - **[Windows ML](./new-windows-ml/overview.md)** — Hardware-accelerated inference on CPU, GPU, or NPU. Any Windows hardware.
- **Other**
  - **[Microsoft Foundry](./cloud-ai.md)** — Cloud-hosted frontier models (GPT-4o, DALL-E, etc.) via REST API. Combine with Foundry Local for on-device/cloud fallback.

## Terminology decoder

The Windows AI space has gone through rapid rebranding. Here's a translation table for terms you might encounter:

| Term you've seen | What it means now |
|---|---|
| **Copilot Runtime APIs** | Old name (2024) for **Windows AI APIs**. Same functionality, renamed. |
| **Windows Copilot Runtime** | Old umbrella term (2024) for the AI features now called **Microsoft Foundry on Windows**. |
| **Windows AI Foundry** | Old umbrella term (2025) for the AI features now called **Microsoft Foundry on Windows**. |
| **Microsoft Foundry on Windows** | Current umbrella brand covering Windows AI APIs + Foundry Local + Windows ML. |
| **Microsoft Foundry** | Microsoft's cloud-based AI platform. Different product, different team, similar name. |
| **Phi Silica** | The specific Phi model optimized for and built into Windows on Copilot+ PCs (NPU). Also available on non-Copilot+ PCs with a supported NVIDIA or AMD GPU. Accessed via Windows AI APIs. |
| **Phi** (general) | Microsoft's family of small language models. Phi-4-mini etc. are available on Azure AI and via Foundry Local. Phi Silica is the Windows inbox version, optimized for NPU and supported GPUs. |
| **Windows ML** (old) | Legacy WinRT-based inference API, inbox since Windows 10 1809. Still works; no new investment. |
| **Windows ML** (new) | New ONNX Runtime-based NuGet package. Current and actively developed. |
| **DirectML** | No longer being actively developed (in sustained engineering). Low-level DirectX 12 ML API for GPU/NPU acceleration. |
| **Windows ML IHV-specific Execution Providers** | The replacement for DirectML, achieving higher performance by working natively with Windows hardware. |
| **Copilot+ PC** | A PC category defined by hardware: NPU with 40+ TOPS, 16GB+ RAM, specific SoCs. Required for most Windows AI APIs; a growing number of APIs (Phi Silica on GPU, Speech Recognition, Video Super Resolution) also run on non-Copilot+ Windows 11 PCs. Not required for Foundry Local or Windows ML. |
| **NPU** | Neural Processing Unit — dedicated AI acceleration hardware in Copilot+ PCs. On a Copilot+ PC, Windows AI APIs route inference through the NPU automatically; some APIs also support running on GPU or CPU on non-Copilot+ devices. |

## Combine options in the same app

These options aren't mutually exclusive. A typical pattern for a resilient AI feature falls back across three tiers: first try **Windows AI APIs** for the fastest, most optimized experience on supported hardware; if unavailable, fall back to **Foundry Local** to run an open-source model on any Windows hardware; and if that's also unavailable, fall back to **Azure AI** in the cloud. The following example demonstrates this pattern:

```csharp
// 1. Try Windows AI APIs (Copilot+ PC, or supported GPU/CPU for some APIs)
using Microsoft.Windows.AI;
using Microsoft.Windows.AI.Text;

var readyState = LanguageModel.GetReadyState();
if (readyState == AIFeatureReadyState.NotReady)
{
    var deploymentResult = await LanguageModel.EnsureReadyAsync();
    if (deploymentResult.Status == AIFeatureReadyResultState.Success)
    {
        readyState = LanguageModel.GetReadyState();
    }
    else
    {
        // Optional: inspect deploymentResult.ExtendedError for diagnostics.
        // Treat as unavailable so we fall through to Foundry/Azure.
        readyState = AIFeatureReadyState.NotSupportedOnCurrentSystem;
    }
}

if (readyState == AIFeatureReadyState.Ready)
{
    // Use Phi Silica via Windows AI APIs
    using LanguageModel languageModel = await LanguageModel.CreateAsync();
}
// 2. Fall back to Foundry Local (any hardware)
else if (await foundryClient.IsModelAvailableAsync("phi-4-mini"))
{
    // Use Foundry Local OpenAI-compatible API
}
// 3. Fall back to Azure AI (always available)
else
{
    // Use Azure OpenAI
}
```

This pattern gives Copilot+ users the best experience while keeping the feature working on all hardware.

## Still unsure?

- [Microsoft Foundry on Windows overview](./overview.md) — full platform overview
- [AI Dev Gallery](./ai-dev-gallery/index.md) — browse working samples for every API
- [Stack Overflow — windows-ai tag](https://stackoverflow.com/questions/tagged/windows-ai)
