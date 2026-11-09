---
title: "Compile and EPContext with Windows ML CLI"
description: Understand Windows ML CLI compilation, embedded and external EP contexts, deployment dependencies, and provider-specific validation limits.
author: GrantMeStrength
ms.author: jken
ms.topic: article
ms.date: 10/29/2026
---

# Compile and EPContext

When you run `winml compile`, you are not simply copying an ONNX file to a new location. You are asking an execution provider (EP) to transform the model into a form it can load and run directly, without repeating that transformation at every startup. Understanding what the compiler produces — and why — helps you decide when to compile, what output format to choose, and how to balance file size against runtime performance.

Compilation is an offline, one-time step. The artifact it creates is what you ship with your application and what `winml-cli` uses for benchmarking and evaluation.

## What compilation produces

CPU and DirectML don't support offline EPContext compilation through this command. Use the prepared ONNX model directly and let the runtime initialize it. Check `winml compile --list` before adding compilation to a workflow.

For EPs that support ahead-of-time compilation (e.g. `--ep qnn` for Qualcomm NPUs and `--ep vitisai` for AMD NPUs), the compiler goes further. It takes the ONNX graph and produces a binary artifact — the **EP context blob** — that encodes the fully compiled, hardware-ready version of the network. This blob is then associated with the ONNX model file. On subsequent loads, the EP reads the blob rather than re-compiling the graph, which can reduce session initialization work. Measure startup on the target system rather than assuming a particular improvement.

The default compiler backend is `ort` (ONNX Runtime).

## Embedded vs external EPContext

For QNN compilation, `winml-cli` gives you a choice of where the EP context blob lives. By default the blob is written as a sidecar `.bin` file alongside the `.onnx`. Passing `--embed` instead inlines the blob directly into the ONNX file.

**External (default):** The `.onnx` is small and human-inspectable; the heavy binary data lives in a separate file. You must keep the two files together — the ONNX stores a relative path back to the `.bin`. This layout is preferable for version control and for scenarios where you want to inspect or diff the model graph.

**Embedded (`--embed`):** The compiled EP context is stored inside the `.onnx` file rather than in a separate context sidecar. This increases the ONNX file size. The model can still reference external weights, so inspect all dependencies before packaging it.

Neither layout makes the compiled artifact universally portable. Validate its provider, device, runtime, and driver requirements, and preserve relative paths to any external context or weight files.

## Why pre-compile

The first time an ONNX Runtime session is created for a model on a hardware EP, the runtime must partition the graph, allocate buffers, and JIT-compile the operators. On an NPU this process can take several seconds. For applications with tight startup budgets — on-device inference in a UI flow, for example — that cold-start cost is often unacceptable.

A model produced by `winml compile` has already paid that cost. The EP context blob is the result of compilation, not its input. When the application loads the compiled model, the EP can reuse the prepared context. Loading still requires runtime setup, allocations, and any provider-specific initialization. Validate deployment compatibility and startup time separately.

If you are iterating on quantization settings or ONNX graphs and want to check whether the model compiles at all, pass an already-quantized (QDQ) model directly — `winml compile` compiles whatever ONNX file you supply and does not have a separate quantization pass to skip.

## Skipping validation

For compatible multi-model workflows, repeat `-m` and select `--compiler ort_session --output-dir` to create a shared context. Keep all referenced graphs and context files together. See [Compile command](../commands/compile.md) for the syntax and restrictions.

By default `winml compile` runs a validation pass after compilation finishes — it loads the compiled model into an inference session, feeds it dummy inputs (all-ones tensors), and checks that the outputs do not contain NaN or Inf values. This catches basic compilation failures early (e.g., the EP rejecting the graph or producing garbage outputs).

The `--no-validate` flag skips that pass. It is useful during rapid iteration when you only want to confirm that compilation succeeds without the overhead of a trial inference run.

## See also

- [EPs and devices](execution-providers-devices.md) — execution provider selection and `--ep` / `--device` flags
- [Analyze and optimize](analysis-optimization.md) — graph-level analysis before compilation
- [compile command reference](../commands/compile.md)
- [build command reference](../commands/build.md)
