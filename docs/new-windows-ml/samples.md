---
title: Windows ML samples
description: Learn about the different samples available for Windows ML, including LLMs, image generation, object detection, and more.
ms.date: 2/4/2026
ms.topic: article
keywords: windows 11, windows machine learning, WinML, samples, tools, AI Dev Gallery, ONNX models
---

# Windows ML samples

Windows ML supports a wide variety of ONNX models for different AI scenarios. This page provides samples for using Windows ML from different frameworks and languages, as well as ready-to-use model samples for common AI tasks.

## AI Dev Gallery

The [AI Dev Gallery](../ai-dev-gallery/index.md) is the easiest way to explore Windows ML samples. It provides:

- **Interactive demos** for each model scenario
- **Full source code** for every sample
- **One-click export** to a Visual Studio project so you can use the model in your own app

> [!TIP]
> Each model sample in the AI Dev Gallery includes complete source code. Use the **Export to Visual Studio** feature to quickly create a working project with your chosen model.

## Framework integration samples

The [Windows App SDK Samples repository on GitHub](https://github.com/microsoft/windowsappsdk-samples) contains sample applications that demonstrate how to integrate Windows ML into different languages and frameworks.

| Language | Framework | Packaging type | Dependency type | Link |
|----------|-----------|----------------|-----------------|------|
| C# | Console | Unpackaged | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs/CSharpConsoleDesktop) |
| C# | WPF | Unpackaged | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs-wpf) |
| C# | WinUI | MSIX | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs-winui) |
| C# | WinForms | Unpackaged | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cs-winforms) |
| C++ | Console | DLL w/ separate EXE | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cpp/CppConsoleDll) |
| C++ | Console | Unpackaged | Self-contained | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cpp/CppConsoleDesktop.SelfContained) |
| C++ | Console | MSIX | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/cpp/CppConsoleDesktop.FrameworkDependent) |
| Python | Console | Unpackaged | Framework-dependent | [Link](https://github.com/microsoft/WindowsAppSDK-Samples/tree/main/Samples/WindowsML/python) |

## Model samples by scenario

The following sections list available models for each AI scenario, along with their size and hardware support. Click "Try it yourself" to open the sample in the AI Dev Gallery app.

### Large Language Models (LLMs) - Text generation

Generate conversational text responses using state-of-the-art language models.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/chat)

| Model | Hardware | Size |
|-------|----------|------|
| Phi 4 Mini CPU | CPU | 4.6 GB |
| Phi 3.5 Mini CPU ACC4 | CPU | 2.6 GB |
| Phi 3 Mini CPU | CPU | 2.5 GB |
| Phi 3 Mini CPU ACC4 | CPU | 2.5 GB |
| Phi 3 Medium CPU ACC4 | CPU | 8.6 GB |
| Mistral 7B Instruct 0.2 CPU | CPU | 4.6 GB |
| Mistral 7B Instruct 0.2 CPU ACC4 | CPU | 4.6 GB |

### Large Language Models (LLMs) - Multi-modal

Analyze images and generate text descriptions using vision-enabled language models.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/describe-image)

| Model | Hardware | Size |
|-------|----------|------|
| Phi 3 Vision CPU | CPU | 3.0 GB |
| Phi 3.5 Vision CPU | CPU | 3.0 GB |

### Text embedding (Semantic search)

Convert text into vector embeddings for semantic search and similarity matching.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/semantic-search)

| Model | Hardware | Size |
|-------|----------|------|
| all-MiniLM-L12-v2 | CPU, GPU | 127.2 MB |
| all-MiniLM-L6-v2 | CPU, GPU | 86.4 MB |

### Image generation

Create images from text prompts using diffusion models.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/generate-image)

| Model | Hardware | Size |
|-------|----------|------|
| Stable Diffusion v1.4 | CPU, GPU | 5.1 GB |

### Image classification

Classify images into predefined categories.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/classify-image)

| Model | Hardware | Size |
|-------|----------|------|
| MobileNet v2 1.0 | CPU, GPU | 13.3 MB |
| ResNet101 v1 7 | CPU, GPU | 170.6 MB |
| ResNet50 v1 7 | CPU, GPU | 97.8 MB |
| SqueezeNet 1.1 | CPU, GPU, NPU | 4.7 MB |

### Image object detection

Detect and locate objects within images.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/detect-objects)

| Model | Hardware | Size |
|-------|----------|------|
| Faster RCNN 10 | CPU, GPU | 159.6 MB |
| Faster RCNN 12 | CPU, GPU | 168.5 MB |
| YOLOv4 | CPU, GPU | 245.5 MB |

### Image segmentation (Background detection)

Separate foreground subjects from backgrounds in images.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/detect-background)

| Model | Hardware | Size |
|-------|----------|------|
| SINet | CPU, GPU, NPU | 404.7 KB |

### Human pose detection

Detect human body poses and key points in images.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/detect-pose)

| Model | Hardware | Size |
|-------|----------|------|
| HRNet Pose | CPU, GPU, NPU | 108.9 MB |

### Street segmentation

Segment street photographs into detectable zones for autonomous driving and mapping applications.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/segment-street)

| Model | Hardware | Size |
|-------|----------|------|
| FFNet 78s | CPU, GPU, NPU | 104.9 MB |
| FFNet 54s | CPU, GPU, NPU | 68.8 MB |

### Face detection

Detect and locate human faces in images.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/detect-face)

| Model | Hardware | Size |
|-------|----------|------|
| FaceDetLite | CPU, GPU, NPU | 3.4 MB |

### Audio transcription (Voice-to-text)

Convert spoken audio into written text using speech recognition models.

[Try it yourself in the AI Dev Gallery app](aidevgallery://scenarios/transcribe-audio)

| Model | Hardware | Size |
|-------|----------|------|
| Whisper Tiny CPU | CPU | 73.8 MB |
| Whisper Small CPU | CPU | 422.5 MB |
| Whisper Medium CPU | CPU | 1.3 GB |

## See also

- [AI Dev Gallery](../ai-dev-gallery/index.md)
- [Get started with Windows ML](get-started.md)