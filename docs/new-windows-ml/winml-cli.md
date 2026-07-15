---
title: Windows ML CLI
description: Use Windows ML CLI to convert, optimize, compile, and validate portable AI models for Windows ML across major Windows hardware targets.
author: andrewleader
ms.author: aleader
ms.topic: overview
ms.date: 07/14/2026
---

# Windows ML CLI

[Windows ML CLI](https://microsoft.github.io/winml-cli/latest/) is a command-line tool for building portable, performant, and high-quality AI models for Windows ML. You can start from a source model on Hugging Face or from your own pipeline, then produce a hardware-optimized artifact in a reproducible workflow.

Windows ML CLI handles conversion, graph optimization, and compilation across AMD, Intel, NVIDIA, and Qualcomm targets. You can also use it in CI/CD pipelines to validate models and prepare them for release.

## What you can do

- **Build once, run across hardware** - Use primitive commands such as `export`, `analyze`, `optimize`, `quantize`, and `compile`, or use `winml build` to generate a workflow that produces portable models for Windows hardware.
- **Inspect each stage** - Review operator compatibility, shape mismatches, graph optimizations, and execution-provider-aware tuning throughout the pipeline.
- **Automate model preparation** - Integrate CLI-driven workflows with your existing build and release automation.

## What you get out of the box

- **Windows ML execution provider support** - Use the same commands across supported execution providers.
- **Curated model catalog** - Start from a verified list of [supported models](https://microsoft.github.io/winml-cli/latest/reference/supported-models/) that run across Windows ML execution providers.
- **Bring your own ONNX model** - Analyze and optimize an existing ONNX model, even if you did not convert it from PyTorch.

## Learn more

- [Windows ML CLI documentation](https://microsoft.github.io/winml-cli/latest/)
- [Supported models for Windows ML CLI](https://microsoft.github.io/winml-cli/latest/reference/supported-models/)
- [Find or train models](./models.md)
