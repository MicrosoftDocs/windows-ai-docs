---
title: What is Microsoft Foundry on Windows?
description: Guidance to help developers get started using the AI features, tools, and capabilities available on Windows.
ms.date: 11/17/2025
ms.topic: overview
no-loc: [Microsoft Foundry on Windows, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# What is Microsoft Foundry on Windows?

**Microsoft Foundry on Windows** provides a number of AI-backed features and APIs for Copilot+ PCs that run locally and in the background at all times.

Microsoft Foundry on Windows includes several components that support unique AI experiences:

1. [Windows AI APIs](apis/index.md): Use built-in Windows AI APIs to integrate AI services into your Windows apps. These APIs include the following:
    - [Phi Silica](./apis/phi-silica.md): A local, ready-to-use language model.
    - Language model customization: Fine-tune the Phi Silica model with Low Rank Adaptation ([LoRA](./apis/phi-silica-lora.md)).
    - [Imaging](./apis/imaging.md): Scale and sharpen an image ([Image Super Resolution](./apis/imaging.md#what-can-i-do-with-image-super-resolution)), generate text that describes an image ([Image Description](./apis/imaging.md#what-can-i-do-with-image-description)), identify objects within an image ([Image Segmentation](./apis/imaging.md#what-can-i-do-with-image-segmentation)), remove objects from an image ([Object Erase](./apis/imaging.md#what-can-i-do-with-object-erase)), and generate images from prompt text ([Image Generation](apis/image-generation.md)).
    - [Video Super Resolution (VSR)](apis/video-super-resolution.md): Upscale low-resolution video streams.
    - [Text Intelligence](./apis/phi-silica.md#text-intelligence-skills): Detect and extract text within images and convert it into machine readable character streams ([Text Recognition](./apis/text-recognition.md)), format the prompt response into a structured table format, summarize prompt text and conversations, and rewrite prompt text to optimize clarity, readability, and tone (or style).
    - [App Content Search](./apis/app-content-search.md): Index in-app content to make it searchable by keyword or semantic meaning, and build a local Retrieval Augmented Generation (RAG) pipeline and on-device knowledge base for a local or cloud based Large Language Model (LLM).

2. [Foundry Local](./foundry-local/get-started.md): popular OSS models that you can leverage and pull into your app
3. [Windows ML](./new-windows-ml/overview.md): enables AI inferencing with your own ONNX model
4. Dev Tools: tooling such as [Visual Studio AI Toolkit](./toolkit/toolkit-getting-started.md) and [AI Dev Gallery](./ai-dev-gallery/index.md) that will help you be successful building AI capabilities

:::image type="content" source="images/ms-foundry-on-windows-overview.png" alt-text="A diagram showing the various components that comprise Microsoft Foundry on Windows (Windows AI APIs, Foundry Local, and Windows ML).":::

## How can you use AI in your Windows app?

A few ways that Windows apps can leverage Machine Learning (ML) models to enhance their functionality and user experience with AI, include:

- Apps can use Generative AI models to understand complex topics to summarize, rewrite, report on, or expand.
- Apps can use models that transform free-form content into a structured format that your app can understand.
- Apps can use Semantic Search models that allow users to search for content by meaning and quickly find related content.
- Apps can use natural language processing models to reason over complex natural language requirements, and plan and execute actions to accomplish the user's ask.
- Apps can use image manipulation models to intelligently modify images, erase or add subjects, upscale, or generate new content.
- Apps can use predictive diagnostic models to help identify and predict issues and help guide the user or do it for them.

## Using Windows AI APIs versus bringing your own models

### Use Windows AI APIs

When a local AI model is the right solution, you can use [Windows AI APIs](./apis/index.md) to integrate AI services for users on Copilot+ PCs. These APIs are built-in on your PC and enable unique AI-powered features with relatively little overhead.

### Train your own model

If you have the ability to train your own model using your own private data with platforms like [TensorFlow](https://www.tensorflow.org/) or [PyTorch](https://pytorch.org/). You can integrate that custom model into your Windows application by running it locally on the device hardware using [ONNX Runtime](https://onnxruntime.ai/) and AI Toolkit for Visual Studio Code.

**[AI Toolkit for Visual Studio Code](./toolkit/index.md)** is a VS Code Extension that enables you to download and run AI models locally, including access to hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md). The AI Tookit can also help you with:

- Testing models in an intuitive playground or in your application with a REST API.
- Fine-tuning your AI model, both locally or in the cloud (on a virtual machine) to create new skills, improve reliability of responses, set the tone and format of the response.
- Fine-tuning popular small-language models (SLMs), like [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) and [Mistral](https://mistral.ai/).
- Deploy your AI feature either to the cloud or with an application that runs on a device.
- Leverage hardware acceleration for better performance with AI features using DirectML. DirectML is a low-level API that enables your Windows device hardware to accelerate the performance of ML models using the device GPU or NPU. Pairing DirectML with the ONNX Runtime is typically the most straightforward way for developers to bring hardware-accelerated AI to their users at scale. Learn more: [DirectML Overview](./directml/dml.md).
- Quantize and validate a model for use on NPU by using the model conversion capabilities

You may also want to look into these [model fine-tuning concepts](fine-tuning.md) to adjust a pre-trained model to better fit your data.

### Using Cloud AI Models

If using local AI features isn't the right path for you, [using Cloud AI models and resources](./cloud-ai.md) can be a solution.

### Other AI Features

1. [App Actions on Windows](./app-actions/index.md): create actions for your app enabling new and unique AI capabilities for consumers

## Use Responsible AI practices

Whenever you are incorporating AI features in your Windows app, we **highly** recommend following the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance.
