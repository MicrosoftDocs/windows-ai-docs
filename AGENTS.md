# Windows AI Docs — Agent Instructions

This file is read automatically by GitHub Copilot CLI, VS Code Copilot, and the GitHub Copilot coding agent. It provides context for AI agents working in the `windows-ai-docs-pr` repository.

## What this repo is

This is the **private preview** source for Windows AI documentation published at [learn.microsoft.com/windows/ai/](https://learn.microsoft.com/windows/ai/). Changes here go through review before publishing.

> [!IMPORTANT]
> **API reference content is NOT in this repo.** API reference for Windows ML, DirectML, and related APIs lives in separate repositories. If a user asks you to edit API reference pages (namespaces, classes, methods, properties), stop and direct them to the docs team at jkendirs@microsoft.com.

## Directory structure

| Directory | Content |
|-----------|---------|
| `docs/` | **All content lives here** |
| `docs/models/` | Getting started with AI models on Windows |
| `docs/directml/` | DirectML — GPU-accelerated machine learning |
| `docs/windows-ml/` | Windows ML (WinML) runtime |
| `docs/new-windows-ml/` | New Windows ML (successor to WinML) |
| `docs/foundry-local/` | Foundry Local — on-device model hosting |
| `docs/mcp/` | Model Context Protocol on Windows |
| `docs/app-actions/` | App Actions / Windows agent platform |
| `docs/agent-launchers/` | Agent launcher APIs |
| `docs/toolkit/` | AI toolkit and dev tools |
| `docs/ai-dev-gallery/` | AI Dev Gallery samples |
| `docs/npu-devices/` | NPU hardware and Copilot+ PC guidance |
| `docs/directml/` | DirectML compute pipeline |

All new and updated content goes under `docs/` in the appropriate subdirectory.

## Branch naming

Always create a new branch before making changes. Never commit directly to `main`.

Pattern: `{your-alias}-main/{brief-description}`

Examples:
- `jken-main/fix-foundry-local-quickstart`
- `pmsmith-main/update-directml-samples`
- `aliasname-main/add-npu-overview`

## Pull request requirements

- **Target branch:** `main`
- **PR title:** brief, imperative ("Fix broken link in models quickstart", "Add Foundry Local overview")
- **PR description:** include the `learn.microsoft.com` URL(s) affected, and a one-line summary of what changed and why
- **After opening PR:** email jkendirs@microsoft.com so the docs team can review

## Required YAML front-matter

Every `.md` file must have this metadata block at the top:

```yaml
---
title: Title here (10–65 characters)
description: One-sentence summary (115–145 characters, no quotes)
author: GrantMeStrength
ms.author: jken
ms.topic: overview
ms.date: MM/DD/YYYY
---
```

Common `ms.topic` values: `overview`, `how-to`, `tutorial`, `article`, `reference`

When updating an existing file, always update `ms.date` to today's date.

## AI terminology — use these exact names

Getting terminology right is especially important in AI documentation.

| Use this | Not this |
|----------|----------|
| Windows ML | WinML (in prose; `WinML` is acceptable in code) |
| DirectML | Direct Machine Learning or DirectML API |
| Foundry Local | Azure AI Foundry Local or just Foundry |
| Copilot+ PC | Copilot Plus PC, Copilot+PC |
| NPU | npu, Neural Processing Unit (spell out only on first use) |
| on-device AI | on-device inference, edge AI |
| ONNX Runtime | OnnxRuntime, onnxruntime (in prose) |
| Model Context Protocol | MCP (spell out on first use per page) |
| Windows App SDK | WinAppSDK, WASDK |

## Writing style rules

- **Active voice, present tense, second person** — "you load the model", not "the model is loaded"
- **Neutral and instructional tone** — no marketing language ("powerful", "seamless", "groundbreaking")
- **Short paragraphs** — 3–4 sentences maximum
- **Procedural steps** — 7 or fewer; consolidate trivial actions into one step
- **Code blocks** — always include a language tag (` ```csharp `, ` ```cpp `, ` ```python `, ` ```console `)
- **Images** — always include descriptive `alt` text; never generate AI images
- **Version numbers** — always state minimum Windows version (e.g., "Requires Windows 11, version 24H2 or later")
- **Copilot+ PC requirement** — if a feature requires a Copilot+ PC or NPU, call it out clearly in a NOTE or prerequisites section

## What you can change

- ✅ Conceptual `.md` files anywhere under `docs/`
- ✅ Updating outdated API names, version numbers, or step-by-step instructions
- ✅ Fixing broken links (use relative paths within the repo when linking to other articles)
- ✅ Adding code examples (C#, C++, or Python, with language tags and explanatory comments)
- ✅ Fixing typos and grammar
- ✅ Updating YAML metadata (`ms.date`, `description`, `title`)

## What you must NOT change

- ❌ `.openpublishing.redirection.json` — redirects require special review
- ❌ `docfx.json` — repo configuration; do not touch
- ❌ `TOC.yml` files — table of contents changes require docs team approval
- ❌ API reference stubs — these are in separate repos; contact jkendirs@microsoft.com

## Publish timing

- Merges to `main` publish **nightly at approximately 10 PM Pacific time**
- Preview your changes at `review.learn.microsoft.com` before the PR is merged
- For urgent changes, email jkendirs@microsoft.com

## Pre-written prompts

Use these prompts with GitHub Copilot CLI or VS Code Copilot. Fill in the bracketed placeholders.

### Fix outdated content

```
The page at [URL] has outdated information. Specifically: [describe what is wrong and what the correct information is].

Find the markdown source file in this repo, make only the necessary changes, create a branch named [alias]-main/fix-[description], commit the changes with a clear message, and open a pull request targeting main. Include the affected URL in the PR description.
```

### Add missing information

```
The page at [URL] is missing information about [topic].

The new section should cover: [describe the content]
Source / authoritative reference: [spec link, header file path, or description of correct behavior]

Find the markdown source file, add a new H2 or H3 section in the appropriate place, create a branch named [alias]-main/add-[description], commit, and open a PR targeting main.
```

### Fix a broken link

```
There is a broken link on the page at [URL]. The broken link text is "[link text]" and it should point to [correct destination or describe what it should link to].

Find the markdown file, fix the link, create a branch named [alias]-main/fix-broken-link, commit, and open a PR.
```

### Create a new overview topic

```
Create a new conceptual overview topic for [feature or concept name].

Audience: Windows developers (intermediate level)
Key points to cover: [list the main things the page should explain]
Related pages to link to: [URLs of related articles]
Minimum Windows version required: [e.g., Windows 11 24H2]
Copilot+ PC / NPU required: [yes/no — if yes, add a NOTE callout]
File location: docs/[appropriate-subdirectory]/[filename].md

Generate the markdown file with proper YAML front-matter (title 10–65 chars, description 115–145 chars, author: GrantMeStrength, ms.author: jken, ms.topic: overview, ms.date: today's date). Use active voice, present tense, second person. No marketing language. Include at least one C# or Python code example with a language tag and explanatory comments. Create a branch named [alias]-main/add-[description], commit, and open a PR targeting main.
```

## Contacts

| Area | Contact |
|------|---------|
| Windows AI docs | John Kennedy — jkendirs@microsoft.com |
| Drivers / Server docs | Robin Harwood |

For questions about API reference repos, contact jkendirs@microsoft.com — do not attempt to edit those files directly.
