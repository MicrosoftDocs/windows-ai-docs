---
title: "Contributing with Windows ML CLI"
description: Find Windows ML CLI contributor setup, validation guidance, and documentation ownership before preparing a code or content contribution.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Contributing

For the full contributing guide — development setup, coding conventions, testing, PR checklist, and CLA — see [`CONTRIBUTING.md`](https://github.com/microsoft/winml-cli/blob/main/CONTRIBUTING.md) in the repository root.

## Quick Reference

```powershell
# Clone and set up
git clone https://github.com/microsoft/winml-cli.git
cd winml-cli
uv sync
uv run pre-commit install

# Download runtime check rules (required for `winml analyze`)
gh release download <tag> --repo microsoft/winml-cli --pattern 'rules-v*.zip' --dir .
Expand-Archive -Path .\rules-v*.zip -DestinationPath src\winml\modelkit\analyze\rules\runtime_check_rules -Force

# Run tests
uv run pytest tests/ -m "not e2e and not npu and not gpu"

# Lint and format
uv run ruff check src/ tests/ --fix
uv run ruff format src/ tests/

```

## Contribute to the documentation

The Windows ML CLI documentation in Microsoft Learn lives in the Windows AI documentation repository, separately from the CLI implementation. Use the edit link on the Learn article or coordinate with the Windows AI docs team for repository access.

When changing CLI behavior, update the corresponding command reference, concepts, and examples in the documentation as part of the same release workflow. Don't use the CLI repository's old MkDocs publishing commands to publish Learn content. See the [Microsoft Learn contributor guide](/contribute/content/) for the contribution process.

## See also

- [Add model support with an AI agent](getting-started/agent-skill/adding-model-support.md) — use
  the contribution skill to diagnose, implement, validate, and review support
  for one model
- [Installation](getting-started/installation.md) — user-facing setup
- [Commands](commands/overview.md) — CLI reference
