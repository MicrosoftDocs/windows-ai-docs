---
title: "Use Windows ML CLI with an AI Agent"
description: Use the Windows ML CLI workflow skill with a compatible AI agent to inspect models, prepare configurations, and validate build artifacts.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Use Windows ML CLI with an AI Agent

Windows ML CLI ships a **Copilot Skill** (`use-winml-cli`) that lets AI coding agents
drive the entire model-building pipeline on your behalf. When a coding agent has
this skill attached, it can inspect models, generate configs, run builds, and
interpret results — without you having to remember exact flags or stage ordering.

---

## What the skill provides

The skill teaches the agent:

| Capability | What the agent learns |
|---|---|
| **Pipeline shape** | The stage order (`inspect → export → analyze → optimize → quantize → compile → perf`) and when to enter mid-pipeline |
| **Flag discovery** | Always run `winml <command> --help` before quoting a command — never fabricate flags |
| **Output mapping** | Which command's `-o` produces the artifact the user actually needs |
| **Scope awareness** | Check the current model catalog and distinguish supported single-model, composite, and recipe-specific GenAI workflows |
| **Hardware detection** | Use `winml sys --list-ep` to confirm what's available before targeting an EP |
| **Two paths** | When to use primitives (debugging, exploring) vs. config + build (production, CI) |

---

## How to use it

### With GitHub Copilot Coding Agent

To make the [Copilot Coding Agent](https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/overview)
(the cloud agent that creates PRs) follow the skill's guidance, reference it in
`.github/copilot-instructions.md`. The Coding Agent reads that file automatically
when working on this repository.

### With other AI agents

For agents that support custom instructions (e.g., Copilot Extensions, Claude,
ChatGPT with file uploads, or custom MCP tool servers), attach the skill file
as context:

```text
skills/use-winml-cli/SKILL.md
```

You can copy the file contents into your agent's system prompt, upload it as a
reference document, or include it in a `.github/copilot-instructions.md` for
VS Code Copilot Chat. The skill uses standard markdown with YAML front-matter —
any agent that accepts text context can benefit from it.

---

## Skill location

```text
winml-cli/
└── skills/
    └── use-winml-cli/
        └── SKILL.md          ← the skill definition
```

---

## Example agent interaction

```text
User: Can I run ConvNeXt on my Snapdragon X Elite NPU?

Agent (with skill):
1. Runs `winml sys --list-ep` → confirms QNNExecutionProvider is registered
2. Runs `winml inspect -m facebook/convnext-tiny-224` → checks model support
3. Runs `winml config -m facebook/convnext-tiny-224 --device npu --ep qnn --compile -o config.json`
4. Runs `winml build -c config.json -m facebook/convnext-tiny-224 -o output/`
5. Runs `winml perf -m output/model.onnx --device npu --ep qnn --monitor`
6. Runs an appropriate `winml eval` workflow and reports performance and quality separately
```
