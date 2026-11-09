---
title: "Tutorials with Windows ML CLI"
description: Choose a Windows ML CLI tutorial to prepare a Hugging Face model for a Qualcomm NPU or optimize and validate an existing ONNX graph.
author: GrantMeStrength
ms.author: jken
ms.topic: overview
ms.date: 10/29/2026
---

# Tutorials

Tutorials are linear, prescriptive, end-to-end walkthroughs that guide you through building something concrete with `winml-cli`. Each tutorial moves in one direction—start to finish—so you can follow along without making decisions. If you need to understand the reasoning behind a feature, see the Concepts section (the why and when). If you need a quick reference for a specific command, see Commands (the what). Tutorials sit alongside Samples, which are reference-style demos that compare multiple approaches side by side rather than walking through a single path.

## Available tutorials

| Tutorial | What you'll build | Hardware |
|---|---|---|
| [Hugging Face Model to NPU](prepare-image-classification-model.md) | Prepare, calibrate, compile, and validate a ConvNeXt classifier | Compatible Qualcomm NPU and QNN provider |
| [Bring Your Own ONNX Model](build-existing-model.md) | Analyze and optimize an existing ONNX model, then compare quality and performance | CPU baseline; optional target-specific preparation |

For other model layouts, see the [CLIP composite sample](../samples/clip-composite.md)
and [Qwen3 GenAI bundle sample](../samples/generative-model-bundle.md).
