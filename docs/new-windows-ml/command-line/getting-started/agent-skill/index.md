---
title: "Use Windows ML CLI with AI Agents"
description: Choose Windows ML CLI agent skills for supported model workflows, adding model support, or investigating performance on a target device.
author: GrantMeStrength
ms.author: jken
ms.topic: overview
ms.date: 11/12/2026
---

# Use Windows ML CLI with AI Agents

This section documents three complementary agent workflows: using the CLI to
build and run models, contributing missing model support, and investigating
ONNX inference latency.

## Choose a skill

| Goal | Skill | Guide |
|---|---|---|
| Build, optimize, compile, benchmark, or troubleshoot a supported model | `use-winml-cli` | [Use Windows ML CLI with an AI agent](model-preparation-agent.md) |
| Add or repair model support and prepare a validated contribution | `adding-model-support` | [Add model support with an AI agent](adding-model-support.md) |
| Investigate latency or validate optimization candidates on an EP/device | `auto-optimize` | [Auto Optimize](auto-optimize.md) |

Use `use-winml-cli` for normal model workflows, including targeting available
CPU, GPU, and NPU execution providers.

Use `adding-model-support` when a model needs a recipe, exporter, resolver,
task mapping, dataset adapter, evaluator, or shared CLI fix. It handles one
model per run and drives the contribution through independent validation and
review.

Use `auto-optimize` for an experimental optimization search or validation of
an existing candidate. It checks correctness before paired performance
measurements and publishes a validated replayable bundle. Routine optimization
with existing CLI features remains part of `use-winml-cli`.

## How to use a skill

Choose a skill from the preceding table and make its complete directory under
[`skills/`](https://github.com/microsoft/winml-cli/tree/main/skills) available
to an agent runtime that supports skills or custom instructions. Repository
source directories are not automatically registered by every host.

### With GitHub Copilot Coding Agent

To make the
[Copilot Coding Agent](https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/overview)
follow the selected skill, reference its `SKILL.md` in
`.github/copilot-instructions.md`. The Coding Agent reads that file
automatically when it works in the repository.

### With other AI agents

For agents that support skills, install the complete selected skill directory.
For agents that support custom instructions, attach the skill entry point as
context:

```text
skills/use-winml-cli/SKILL.md
skills/adding-model-support/SKILL.md
skills/auto-optimize/SKILL.md
```

You can reference the selected file from custom instructions or upload it as a
reference document. Keep its linked references and scripts available because
some workflows use those files during execution. Then describe your goal in
natural language. Each guide provides example prompts and explains what the
skill handles automatically.

The agent also needs access to a working CLI environment and the target hardware
for runtime measurements. A cloud-hosted agent doesn't inherit your PC's NPU.
Review proposed commands, artifact changes, and validation evidence before
accepting a result; a loaded skill doesn't guarantee a successful deployment.
For loading diagnostics and stale-copy checks, see
[Auto Optimize loading and verification](auto-optimize.md#loading-and-verification).
