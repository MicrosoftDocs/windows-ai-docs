---
title: DirectML Tools
description: The tools in this article are available to enhance DirectML and incorporate it into your AI app.
ms.topic: article
ms.date: 05/21/2024
---

# DirectML Tools 

The following tools are available to enhance DirectML and incorporate it into your AI app.

## ONNX Runtime Go Live (Olive) 

Olive is an easy-to-use hardware-aware model optimization tool that composes industry-leading techniques across model compression, optimization, and compilation. You can pass a model through Olive with DirectML as the target backend and Olive composes the best suitable optimization techniques to output the most efficient model(s). For more information and samples on how to use Olive, please see [Olive’s documentation](https://microsoft.github.io/Olive/).

## DxDispatch 

DxDispatch is simple command-line executable for launching DirectX 12 compute programs without writing all the C++ boilerplate. The input to the tool is a JSON model that defines resources, dispatchables (compute shaders, DirectML operators, and ONNX models), and commands to execute. For more information, please see [the DxDispatch guide on Github](https://github.com/microsoft/DirectML/blob/master/DxDispatch/doc/Guide.md).

## DirectMLX 

DirectMLX is a C++ header-only helper library for DirectML, intended to make it easier to compose individual operators into graphs. For more information, please visit [the DirectMLX documentation](dml-directmlx.md)

## ONNX Runtime Perf Tests 

The onnxruntime perf test is a tool that measures the performance of running ONNX models with different execution providers (EPs) in the onnxruntime framework. It can report metrics such as latency, throughput, memory usage, and CPU/GPU utilization for each EP and model. The onnxruntime perf test can also compare the results of different EPs and models and generate charts and tables for analysis. 

To use the onnxruntime perf test with the directml ep, install the onnxruntime-directml package and specify the directml as the EP in the command line arguments. For example, the following command runs the perf test for the resnet50 model with the directml ep and the default settings: 

```
onnxruntime_perf_test -m resnet50 -e directml
```

The perf test will output the average latency, peak working set memory, and average CPU/GPU utilization for the directml ep and the resnet50 model. One can also use other options to customize the perf test, such as changing the number of iterations, the batch size, the concurrency, the warmup runs, the model inputs, and the output formats. For more details, refer to the [onnxruntime perf test documentation](https://onnxruntime.ai/docs/performance/tune-performance/profiling-tools.html).