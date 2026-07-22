---
title: Windows ML EPs vs. bring-your-own — comparison and tradeoffs
description: Compare the two ways to obtain execution providers in Windows ML — the Windows ML EPs and bring-your-own EPs — and choose the right strategy for your app.
ms.date: 04/08/2026
ms.topic: concept-article
---

# Windows ML EPs vs. bring-your-own: comparison and tradeoffs

Windows ML supports two ways to obtain execution providers (EPs) for hardware-accelerated inference. This page explains both options and helps you choose the right strategy for your app.

## Comparison

| | Windows ML EPs (recommended) | Bring your own (alternative) |
|---|---|---|
| **How** | `ExecutionProviderCatalog` APIs | NuGet packages or standalone EP binaries |
| **Setup complexity** | Low — one API call | Medium — manual NuGet reference per EP |
| **Certification** | Yes — Windows-certified, rigorous regression testing process | Depends on the EP |
| **App size impact** | Low — EPs downloaded to system, not bundled | High — EPs bundled with your app (~80 MB each) |
| **EP updates** | Automatic via Windows Update | Manual — you update when you choose |
| **OS requirement** | Win 11 24H2 (for EPs acquired by ExecutionProviderCatalog APIs)¹ | Depends on EP |
| **Network required on first run** | Yes (if EP not already installed) | Depends on your implementation |
| **Managed device support** | IT policy must permit Windows Update | Depends on your implementation |

¹ DirectML and the ORT CPU EP are always included in Windows ML and require no download on any supported OS version.

## When to use the Windows ML EPs

Use the Windows ML EPs when:

- App download size matters — avoiding bundling 80-120 MB EP packages per vendor
- You want your app to automatically benefit from EP performance improvements without shipping a new release
- Your target users are on consumer or unmanaged Windows 11 24H2+ devices
- You want to use EPs that are certified by the Windows team for compatibility and stability

See [Windows ML EPs](./supported-execution-providers.md) for the list of available EPs.

## When to bring your own EP

Bring your own EPs when:

- Your users may be on managed (enterprise) devices where Windows Update is restricted or disabled
- You have strict control requirements over the exact EP version your app uses
- You are deploying to offline environments or devices without internet access

See [Bring your own EPs](./bring-your-own-eps.md) for step-by-step instructions.

## Mixing the two approaches

You can use the Windows ML EPs as the preferred path and fall back to a bundled EP for the same hardware target. For example, your app can attempt to get QNN via the Windows ML EP catalog and, if that fails (Windows Update restricted, device offline, etc.), fall back to a QNN NuGet package you bundled yourself — ensuring NPU acceleration on managed or offline devices without changing your inference code.

This hybrid approach gives you the best of both worlds:

- Windows ML EP path: smaller app, automatic updates, zero extra distribution work
- Bundled path: guaranteed availability on managed/offline devices

## See also

- [Windows ML EPs](./supported-execution-providers.md)
- [Bring your own EPs](./bring-your-own-eps.md)
- [Accelerate AI models](./accelerate-ai-models.md)
