---
title: Choose your Windows AI solution
description: Scenario-based guidance for choosing between Windows AI APIs, Foundry Local, Windows ML, and Azure AI — including a terminology reference to decode the Windows AI landscape.
ms.date: 03/18/2026
ms.topic: article
keywords: windows ai, phi silica, foundry local, windows ml, copilot plus, windows ai apis, which api, choose
no-loc: [Microsoft Foundry on Windows, Windows AI APIs, Foundry Local, Windows ML, Phi Silica, Copilot+]
---

# Choose your Windows AI solution

The Windows AI landscape has grown quickly and the terminology can be hard to navigate. This page cuts through it: find your scenario, pick your starting point.

> [!TIP]
> The #1 question to answer first: **does your app need to run on all Windows hardware, or only Copilot+ PCs?**
> - **All Windows hardware** → [Foundry Local](#foundry-local) or [Windows ML](#windows-ml)
> - **Copilot+ PCs only** → [Windows AI APIs](#windows-ai-apis-copilot-pcs) (faster, zero setup)
> - **Cloud or hybrid** → [Azure AI](#azure-ai-cloud-hybrid)

## Decision table

| I want to… | Recommended option | Hardware required |
|---|---|---|
| Add a chat or summarization feature to my app with minimal code | [Windows AI APIs — Phi Silica](./apis/phi-silica.md) | Copilot+ PC |
| Generate, describe, or manipulate images on-device | [Windows AI APIs — Imaging](./apis/imaging.md) | Copilot+ PC |
| Run an LLM locally on any Windows PC (no cloud, no NPU needed) | [Foundry Local](./foundry-local/get-started.md) | Windows 10+ (CPU/GPU) |
| On-device speech-to-text on any hardware | [Foundry Local — Whisper](./foundry-local/get-started.md) | Windows 10+ |
| Run a custom or Hugging Face model with hardware acceleration | [Windows ML](./new-windows-ml/overview.md) | Windows 10+ (CPU/GPU/NPU) |
| Low-level GPU/NPU control for a game or graphics-intensive app | [DirectML](./directml/dml.md) | Windows 10+ (GPU) |
| Use large cloud-hosted models (GPT-4o, DALL-E, etc.) | [Azure AI](./cloud-ai.md) | Any (internet required) |
| Combine on-device and cloud AI, fall back gracefully | [Foundry Local + Azure AI](./cloud-ai.md) | Windows 10+ |
| Semantic in-app search over documents or images | [Windows AI APIs — Semantic Search](./apis/app-content-search.md) | Copilot+ PC |
| Fine-tune Phi Silica on my own data | [Windows AI APIs — LoRA](./apis/phi-silica-lora.md) | Copilot+ PC |

## Options in detail

### Windows AI APIs — Copilot+ PCs

**Best for**: New Windows features with the least code, on Copilot+ PCs.

Windows AI APIs are part of the Windows App SDK and give packaged apps access to on-device AI models that are installed and maintained by Windows — you don't ship the model, don't manage its lifecycle, and it's shared across apps. Inference runs on the NPU for near-instant, private, battery-efficient results.

Available AI capabilities include: text generation (Phi Silica), image description, image generation, image segmentation, image super-resolution, object erase, OCR, semantic search, and video super-resolution. See the [full API list](./apis/index.md).

**Requirements**: Copilot+ PC (NPU with 40+ TOPS — Snapdragon X series, Intel Core Ultra 200V series, AMD Ryzen AI 300 series). Your app must be packaged (MSIX or sparse identity).

> [!div class="nextstepaction"]
> [Get started with Windows AI APIs](./apis/get-started.md)

---

### Foundry Local

**Best for**: LLM or speech-to-text features on any Windows hardware (no Copilot+ required).

Foundry Local runs 20+ open-source models locally via an OpenAI-compatible REST API. You can reuse existing AI code with zero changes — just point your `OpenAIClient` at your locally running Foundry Local OpenAI-compatible endpoint instead of the cloud (see the Foundry Local docs for endpoint details). Models are managed by Foundry Local (not your app), and run on CPU, GPU, or NPU depending on what the device has.

```bash
winget install Microsoft.AIFoundry.Local
foundry model run phi-4-mini
```

**Requirements**: Windows 10 and later. Performance scales with available GPU/NPU.

> [!div class="nextstepaction"]
> [Get started with Foundry Local](./foundry-local/get-started.md)

---

### Windows ML

**Best for**: Running custom, fine-tuned, or Hugging Face models with full control over the inference pipeline.

Windows ML (the new ONNX Runtime-based version) lets you bring your own model in ONNX format and run it locally with hardware acceleration on CPU, GPU, or NPU. It's the right choice when the ready-to-use models in Windows AI APIs and Foundry Local don't cover your scenario, or when you need to integrate a proprietary model.

> [!NOTE]
> There are two APIs named "Windows ML." The **legacy WinRT Windows ML** is the older, inbox API available since Windows 10 1809. The **new Windows ML (NuGet)** is the current ONNX Runtime-based version with better performance and hardware support. New projects should use the [new Windows ML](./new-windows-ml/overview.md).

**Requirements**: Windows 10 and later. Model compatibility and performance vary by hardware.

> [!div class="nextstepaction"]
> [Get started with Windows ML](./new-windows-ml/get-started.md)

---

### Azure AI (cloud and hybrid)

**Best for**: Large frontier models (GPT-4o, DALL-E, Claude, Gemini), scenarios requiring the latest model capabilities, or apps where local hardware can't be assumed.

Azure AI gives you access to the full range of hosted AI models via standard REST APIs. It's the right choice when your scenario requires capabilities beyond what on-device models offer, or when you're building a service rather than a client app.

You can combine Azure AI with Foundry Local in the same app — running fast, private inference locally when the device supports it, and falling back to Azure when it doesn't.

> [!div class="nextstepaction"]
> [Use cloud AI with Windows apps](./cloud-ai.md)

---

## Terminology decoder

The Windows AI space has gone through rapid rebranding. Here's a translation table for terms you might encounter:

| Term you've seen | What it means now |
|---|---|
| **Copilot Runtime APIs** | Old name (2024) for **Windows AI APIs**. Same functionality, renamed. |
| **Windows Copilot Runtime** | Old umbrella term for the AI features now called **Microsoft Foundry on Windows**. |
| **Microsoft Foundry on Windows** | Current umbrella brand covering Windows AI APIs + Foundry Local + Windows ML. |
| **Windows AI Foundry** | Informal shorthand for Microsoft Foundry on Windows. Not the same as **Azure AI Foundry**. |
| **Azure AI Foundry** | Microsoft's cloud-based AI platform. Different product, different team, similar name. |
| **Phi Silica** | The specific Phi model optimized for and built into Windows on Copilot+ PCs. Accessed via Windows AI APIs. |
| **Phi** (general) | Microsoft's family of small language models. Phi-4-mini etc. are available on Azure AI and via Foundry Local. Phi Silica is the NPU-optimized Windows inbox version. |
| **Windows ML** (old) | Legacy WinRT-based inference API, inbox since Windows 10 1809. Still works; no new investment. |
| **Windows ML** (new) | New ONNX Runtime-based NuGet package. Current and actively developed. |
| **DirectML** | Low-level DirectX 12 ML API for GPU/NPU acceleration. Used internally by Windows ML and ONNX Runtime; direct use is for advanced scenarios. |
| **Copilot+ PC** | A PC category defined by hardware: NPU with 40+ TOPS, 16GB+ RAM, specific SoCs. Required for Windows AI APIs; not required for Foundry Local or Windows ML. |
| **NPU** | Neural Processing Unit — dedicated AI acceleration hardware in Copilot+ PCs. Windows AI APIs route inference through the NPU automatically. |

## Combine options in the same app

These options aren't mutually exclusive. A typical pattern for a resilient AI feature:

```csharp
// 1. Try Windows AI APIs (fastest — Copilot+ only)
var readyState = LanguageModel.GetReadyState();
if (readyState == AIFeatureReadyState.EnsureNeeded)
{
    var deploymentResult = await LanguageModel.EnsureReadyAsync();
    if (deploymentResult.Status == PackageDeploymentStatus.CompletedSuccess)
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

if (readyState != AIFeatureReadyState.NotSupportedOnCurrentSystem)
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

- [Microsoft Foundry on Windows overview](./overview.md) — decision tree and full comparison table
- [AI Dev Gallery](./ai-dev-gallery/index.md) — browse working samples for every API
- [Stack Overflow — windows-ai tag](https://stackoverflow.com/questions/tagged/windows-ai)
