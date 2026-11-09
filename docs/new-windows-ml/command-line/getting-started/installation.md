---
title: Install Windows ML CLI
description: Install Windows ML CLI in an isolated Python 3.11 x64 environment, then check the available compute devices and execution providers.
author: GrantMeStrength
ms.author: jken
ms.topic: how-to
ms.date: 10/29/2026
---

# Install Windows ML CLI

## Prerequisites

| Component | Details |
|---|---|
| Windows | Windows 11, version 24H2 or later for NPU workflows |
| Hardware | Device with CPU, GPU, or NPU |
| Python | 3.11, x64 (AMD64), including on Windows on Arm |
| Package manager | [`uv`](https://github.com/astral-sh/uv) |
| Version control | Git, only if you install from source |

> [!NOTE]
> **No NPU?**
>
> You can start with a CPU. Commands that expose `--device auto` select an available device; this doesn't guarantee that a model runs on every provider. Use an explicit CPU target for the [quickstart](quickstart.md).

## Install

Run the following commands in PowerShell from the directory where you want to create the environment:

```powershell
uv python install cpython-3.11-windows-x86_64-none
uv venv --python cpython-3.11-windows-x86_64-none
uv pip install winml-cli
```

These commands install x64 Python 3.11, create a local `.venv` environment, and install the latest published CLI package into it. Use `uv run winml` from this directory without activating the environment. You need an internet connection for package installation and for models or providers that aren't already cached.

> [!WARNING]
> **Windows on Arm requires x64 Python**
>
> The CLI's current dependency set doesn't support native Arm64 Python. The installation commands above explicitly select an x64 interpreter, which runs under emulation on Windows on Arm. Runtime and provider availability still depend on the device and driver.

> [!TIP]
> **Install from source (for development)**
>
> If you want to contribute or run the latest unreleased code:
>
> ```powershell
> git clone https://github.com/microsoft/winml-cli.git
> cd winml-cli
> uv sync --python cpython-3.11-windows-x86_64-none
> ```

## Verify

```powershell
uv run winml --version
uv run winml sys --list-device --list-ep
```

The inventory depends on your hardware and installed providers. Confirm the
expected device and provider are listed, then run the CPU quickstart to validate
model preparation and inference.

The version command identifies the installed distribution. If you run a source
checkout, also record its commit when reproducing an issue; editable source and
installed package metadata can differ.

This command enumerates available compute devices and execution providers on your machine. If an expected device or execution provider is missing, `winml sys` is the right place to diagnose it. See [winml sys](../commands/sys.md) for the full flag reference and troubleshooting tips.

## Next steps

- **[Quickstart](quickstart.md)** — build and benchmark a floating-point CPU baseline.
- **[Hugging Face model to NPU](../tutorials/prepare-image-classification-model.md)** — prepare and validate a model for compatible NPU hardware.
- **[How Windows ML CLI Works](../concepts/how-it-works.md)** — the mental model.
