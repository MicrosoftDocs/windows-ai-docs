---
title: Efficiency Tips for Windows ML Inference
description: Learn how to run Windows ML inference efficiently on CPU and NPU by using EcoQoS, workload type, and memory optimization techniques.
author: dabhattimsft
ms.author: dabhatti
ms.date: 08/13/2026
ms.topic: best-practice
---

# Efficiency tips for Windows ML inference

[Windows ML](./accelerate-ai-models.md) runs machine learning models on your device by pairing the ONNX Runtime with hardware-tuned execution providers (EPs) that target the CPU, GPU, or Neural Processing Unit (NPU). When you run models this way, you often need to minimize power consumption, memory usage, and background resource overhead while maintaining acceptable performance.
This need is especially important for continuously running workloads, where background CPU usage, memory pressure, and battery drain directly affect the user experience.

This article shows you how to apply the efficiency mechanism that matches where the work runs.
For CPU workloads, the OS scheduler and power manager are the primary levers.
For NPU workloads, the most portable lever is to express efficiency intent through the ONNX Runtime so the selected execution provider can map it to device-specific controls.

## CPU workloads

Use [EcoQoS](/windows/win32/procthread/quality-of-service) for background inference that doesn't contribute to the foreground user experience and doesn't require optimal performance.
Don't use EcoQoS for foreground or performance-critical inference.
Opt the process, or preferably the specific inference threads, into EcoQoS by enabling the power-throttling "execution speed" policy.
Use [`SetThreadInformation`](/windows/win32/api/processthreadsapi/nf-processthreadsapi-setthreadinformation) with `ThreadPowerThrottling` (`THREAD_POWER_THROTTLING_STATE`), or [`SetProcessInformation`](/windows/win32/api/processthreadsapi/nf-processthreadsapi-setprocessinformation) with `ProcessPowerThrottling` (`PROCESS_POWER_THROTTLING_STATE`).
For a thread, set `THREAD_POWER_THROTTLING_EXECUTION_SPEED` in both the `ControlMask` and `StateMask` members.
For a process, set `PROCESS_POWER_THROTTLING_EXECUTION_SPEED` in both members.
This setting gives Windows an explicit hint to favor more power-efficient CPU scheduling and performance states for that work.

> [!NOTE]
> Apply EcoQoS to an entire process or to individual threads.
> Use process-level EcoQoS when all work in the process can prioritize efficiency.
> If only specific work should remain efficiency-oriented, apply EcoQoS at the thread level to just those inference threads rather than the entire process.

## NPU workloads

> [!NOTE]
> Windows ML execution providers acquired through the EP catalog require Windows 11, version 24H2 (build 26100) or later.
> NPU inference also requires compatible NPU hardware, a compatible driver, and a [supported NPU execution provider](./supported-execution-providers.md).

Use ONNX Runtime's [`SetEpDynamicOptions()`](https://onnxruntime.ai/docs/api/c/struct_ort_api.html#ab1117a51683e4fbb42687c9db6e8d5fb) to set `ep.dynamic.workload_type=Efficient` on the session, so the selected EP can map the signal to device-specific controls such as scheduling priority and power-saver modes.
Currently, only NPU execution providers act on the `Efficient` signal.

`Efficient` mode can increase latency when higher-priority accelerator workloads are active.
Use it for background work that doesn't have strict latency requirements.

You set `ep.dynamic.workload_type` per ONNX Runtime inference session. If you have multiple sessions, configure each session's efficiency mode independently.

Avoid using hardware-vendor-specific performance knobs (for example, QNN `htp_performance_mode`) directly.
These knobs bypass the OS's intent-based scheduling and power management, and can conflict with system-level policies.
Prefer `ep.dynamic.workload_type` to express efficiency intent so the OS and EP can coordinate appropriately.

## Verify efficiency settings

Check the result of every API call before running inference.
If `SetThreadInformation` or `SetProcessInformation` returns zero, call `GetLastError` and handle the failure.
For process-level EcoQoS, call [`GetProcessInformation`](/windows/win32/api/processthreadsapi/nf-processthreadsapi-getprocessinformation) with `ProcessPowerThrottling` and verify that `PROCESS_POWER_THROTTLING_EXECUTION_SPEED` is present in both the `ControlMask` and `StateMask` members.
For thread-level EcoQoS, check that `SetThreadInformation` succeeds because `GetThreadInformation` doesn't support querying `ThreadPowerThrottling`.
Check the status returned by `SetEpDynamicOptions()` and treat an error as a configuration failure.
To validate the effect of an efficiency setting, measure inference latency, processor or accelerator utilization, and power consumption under a representative workload.

## Memory optimization

If your app is sensitive to memory footprint (for example, an always-on security product), several approaches are available depending on the scenario.

| Approach | What it does | Reference |
|---|---|---|
| Use quantized models (INT8/INT4) | Reduces per-weight storage (for example, FP32 is 4 bytes per weight, INT8 is 1 byte per weight). Actual savings depend on model structure and quantization method. | [ONNX Runtime quantization](https://onnxruntime.ai/docs/performance/model-optimizations/quantization.html) |
| Disable the CPU memory arena (`DisableCpuMemArena()`) | Disables the arena entirely on CPU, so allocations go directly to the system allocator. Trades throughput for lower peak memory. | [DisableCpuMemArena](https://onnxruntime.ai/docs/api/c/struct_ort_api.html#aa2ec3fc24741cfc1024ebb25091dde71) |

## Execution provider-specific performance options

For finer-grained control beyond `ep.dynamic.workload_type`, use EP-specific options exposed through `SetEpDynamicOptions()` or session configuration (for example, QNN's `htp_performance_mode` or OpenVINO's `PERFORMANCE_HINT`).
Prefer the common `ep.dynamic.workload_type` mechanism when possible, because vendor-specific knobs bypass the OS's intent-based scheduling and power management and can conflict with system-level policies.

## Respond to system memory pressure

For processes that host multiple models or need to respond to system memory pressure, consider the following options:

- [`CreateMemoryResourceNotification`](/windows/win32/api/memoryapi/nf-memoryapi-creatememoryresourcenotification): Monitor system-wide memory state. On a low-memory notification, query your memory usage to determine what you can evict—for example, release idle sessions, drop cached arenas, or unload models that aren't actively running inference.
- `MEMORY_PRIORITY_INFORMATION`: Set lower memory priority on idle model sessions by using `SetProcessInformation` with `ProcessMemoryPriority` or `SetThreadInformation` with `ThreadMemoryPriority`. The OS reclaims pages from lower-priority work first under memory pressure. This approach is useful in multi-model scenarios where only one model is active at a time. It doesn't reduce allocations, but it improves system behavior under pressure.

`MEMORY_PRIORITY_INFORMATION` doesn't reduce actual memory consumption. It influences how aggressively the OS trims working-set pages.

## Related content

- [Accelerate AI models with Windows ML](./accelerate-ai-models.md)
- [Select execution providers](./select-execution-providers.md)
- [ONNX Runtime quantization](https://onnxruntime.ai/docs/performance/model-optimizations/quantization.html)
