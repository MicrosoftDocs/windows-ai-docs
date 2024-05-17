---
title: Get started with DirectML
description: Pairing DirectML with the ONNX Runtime is often the most straightforward way for many developers to bring hardward-accelerated AI to their users at scale.
ms.topic: article
ms.date: 05/21/2024
---

# Get Started with DirectML 

Pairing DirectML with the ONNX Runtime is often the most straightforward way for many developers to bring hardware-accelerated AI to their users at scale. These three steps are a general guide for using this powerful combo.

## 1. Convert 

The ONNX format enables you to leverage ONNX Runtime with DirectML, which provides cross-hardware capabilities. 

To convert your model to the ONNX format, you can utilize [ONNXMLTools](https://github.com/onnx/onnxmltools) or [Olive](https://github.com/microsoft/Olive#readme). 

## 2. Optimize 

Once you have an .onnx model, leverage [Olive](https://github.com/microsoft/Olive/tree/main/examples/directml) powered by DirectML to optimize your model. You'll see dramatic performance improvements that you can deploy across the Windows hardware ecosystem. 

## 3. Integrate 

When your model is ready, it's time to bring hardware-accelerated inferencing to your app with ONNX Runtime and DirectML. For Generative AI models, we recommend you use the [ONNX Runtime Generate() API](https://github.com/microsoft/onnxruntime-genai?tab=readme-ov-file)

We built some samples to show how you can use DirectML and the ONNX Runtime: 

* [Phi-3-mini](https://github.com/microsoft/onnxruntime-genai/blob/main/examples/python/phi-3-tutorial.md#run-with-directml) 
* [Large Language Models (LLMs)](https://github.com/microsoft/Olive/tree/main/examples/directml)
* [Stable Diffusion](https://github.com/microsoft/Olive/blob/main/examples/directml/stable_diffusion/README.md)
* [Style transfer](https://github.com/microsoft/onnxruntime-inference-examples/tree/8fcc97e1e035d57ffdfd19b76732e3fc79d8c2a6/c_cxx/fns_candy_style_transfer)
* [Inference on NPUs](https://github.com/microsoft/DirectML/tree/master/Samples/DirectMLNpuInference)

## DirectML and PyTorch

The DirectML backend for Pytorch enables high-performance, low-level access to the GPU hardware, while exposing a familiar Pytorch API for developers. [More information on how to use PyTorch with DirectML can be found here](pytorch-windows.md)

## DirectML for web applications (Preview) 

The Web Neural Network API (WebNN) is an emerging web standard that allows web apps and frameworks to accelerate deep neural networks with on-device hardware such as GPUs, CPUs, or purpose-built AI accelerators such as NPUs. The WebNN API leverages the DirectML API on Windows to access the native hardware capabilities and optimize the execution of neural network models. [For more information on WebNN can be found here](webnn-overview.md)
