---
title: Get started with AI on Windows
description: Guidance to help developers get started using the AI features, tools, and capabilities available on Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 01/21/2025
ms.topic: overview
no-loc: [Windows Copilot Runtime, APIs, AI Toolkit, Studio Effects, Recall, Text Recognition, ONNX Runtime]
ms.custom: [copilot-learning-hub]
---

# Get started with AI on Windows

The ability to build intelligent AI experiences on, and with, Windows is developing rapidly. **Windows Copilot Runtime** offers AI-backed features and APIs on Copilot+ PCs. These features are in active development and run locally in the background at all times. [Learn more about Windows Copilot Runtime](./apis/index.md).

Beyond Windows Copilot Runtime, Microsoft offers a variety of AI services, support, and guidance. To get started and learn how to securely integrate AI for your business needs, explore the guidance in our Windows AI documentation, including:

- [How can you use AI in your Windows app?](#how-can-you-use-ai-in-your-windows-app)
- [Choose between cloud-based and local AI services](#choose-between-cloud-based-and-local-ai-services)
- [Use Windows Copilot Runtime](#use-windows-copilot-runtime)
- [Use Cloud-based APIs](#use-cloud-based-apis)
- [Use a custom model on your local machine](#use-a-custom-model-on-your-local-machine)
- [Use Responsible AI practices](#use-responsible-ai-practices)
- [FAQs](faq.yml)

## How can you use AI in your Windows app?

A few ways that Windows apps can leverage Machine Learning (ML) models to enhance their functionality and user experience with AI, include:

- Apps can use Generative AI models to understand complex topics to summarize, rewrite, report on, or expand.
- Apps can use models that transform free-form content into a structured format that your app can understand.
- Apps can use Semantic Search models that allow users to search for content by meaning and quickly find related content.
- Apps can use natural language processing models to reason over complex natural language requirements, and plan and execute actions to accomplish the user's ask.
- Apps can use image manipulation models to intelligently modify images, erase or add subjects, upscale, or generate new content.
- Apps can use predictive diagnostic models to help identify and predict issues and help guide the user or do it for them.

The easiest way to start integrating AI features into your app is to begin experimenting with **[AI Dev Gallery](../docs/ai-dev-gallery/index.md)**, an open-source app for Windows developers that contains interactive samples with a user-friendly interface that can run models locally using Windows Copilot Runtime, or models downloaded from Hugging Face or GitHub, leveraging your PC's NPU, or if you don't have an NPU, using the GPU or CPU.

## Choose between cloud-based and local AI services

Integrating AI into your Windows application can be achieved through two primary methods: a local model or a cloud-based model. There are several aspects to consider when determining the right option for your needs.

- **Resource Availability**
  - **Local Device:** Running a model depends on the resources available on the device being used, including the CPU, GPU, NPU, memory, and storage capacity. This can be limiting if the device does not have high computational power or sufficient storage. Small Language Models (SLMs), like [Phi](./apis/phi-silica.md), are more ideal for use locally on a device. [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs) offer built-in models run by [Windows Copilot Runtime](./apis/index.md) with ready-to-use AI features.
  - **Cloud:** Cloud platforms, such as [Azure AI Services](/azure/ai-services/), offer scalable resources. You can use as much computational power or storage as you need and only pay for what you use. Large Language Models (LLMs), like the [OpenAI language models](https://platform.openai.com/docs/models), require more resources, but are also more powerful.

- **Data Privacy and Security**
  - **Local Device:** Since data remains on the device, running a model locally can offer benefits regarding security and privacy, with the responsibility of data security resting on the user.
  - **Cloud:** Cloud providers offer robust security measures, but data needs to be transferred to the cloud, which might raise data privacy concerns for the business or app service maintainer in some cases.

- **Accessibility and Collaboration**
  - **Local Device:** The model and data are accessible only on the device unless shared manually. This has the potential to make collaboration on model data more challenging.
  - **Cloud:** The model and data can be accessed from anywhere with internet connectivity. This may be better for collaboration scenarios.

- **Cost**
  - **Local Device:** There is no additional cost beyond the initial investment in the device hardware.
  - **Cloud:** While cloud platforms operate on a pay-as-you-go model, costs can accumulate based on the resources used and the duration of usage.

- **Maintenance and Updates**
  - **Local Device:** The user is responsible for maintaining the system and installing updates.
  - **Cloud:** Maintenance, system updates, and new feature updates are handled by the cloud service provider, reducing maintenance overhead for the user.

## Use Windows Copilot Runtime

When a local AI model is the right solution, you can use [Windows Copilot Runtime](./apis/index.md) features to integrate AI services for users on Copilot+ PCs. A few of these ready-to-use AI features that you can tap into from your Windows app include:

- [**Phi Silica**](./apis/phi-silica.md): a local, ready-to-use language model.
- [**Recall**](./apis/recall.md): a UserActivity API that uses AI to help you search through your past activity, supported by **Click to Do**, a feature that uses Phi Silica to connect actions to the content (text or images) found by Recall.
- [**AI Imaging**](./apis/imaging.md): to scale and sharpen images using AI (**Image Super Resolution**), as well as identify objects within an image (**Image Segmentation**).
- [**Windows Studio Effects**](./studio-effects/index.md): to apply AI effects to the device camera or built-in microphone.

Learn about more about the features available in the [Windows Copilot Runtime Oveview](./apis/index.md).

## Use Cloud-based APIs

If a cloud-based solution works better for your Windows app scenario, you may be interested in some of the tutorials below.

Many APIs are available for accessing cloud-based models to power AI features in your Windows app, whether those models are customized or ready-to-use. Using a cloud-based model can allow your app to remain streamlined by delegating resource-intensive tasks to the cloud. A few resources to help you add cloud-based AI-backed APIs offered by Microsoft or OpenAI include:

- [**Add OpenAI chat completions to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/chatgpt-openai-winui3): A tutorial on how to integrate the cloud-based OpenAI ChatGPT completion capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Add DALL-E to your WinUI 3 / Windows App SDK desktop app**](/windows/apps/how-tos/dall-e-winui3): A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a WinUI 3 / Windows App SDK desktop app.

- [**Create a recommendation app with .NET MAUI and ChatGPT**](./samples/tutorial-maui-ai.md): A tutorial on how to create a sample Recommendation app that integrates the cloud-based OpenAI ChatGPT completion capabilities into a .NET MAUI app.

- [**Add DALL-E to your .NET MAUI Windows desktop app**](./samples/dall-e-maui-windows.md):  A tutorial on how to integrate the cloud-based OpenAI DALL-E image generation capabilities into a .NET MAUI app.

- [**Azure OpenAI Service**](/azure/ai-services/openai/): If you want your Windows app to access OpenAI models, such as GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, DALLE-3 or the Embeddings model series, with the added security and enterprise capabilities of Azure, you can find guidance in this Azure OpenAI documentation.

- [**Azure AI Services**](/azure/ai-services/what-are-ai-services): Azure offers an entire suite of AI services available through REST APIs and client library SDKs in popular development languages. For more information, see each service's documentation. These cloud-based services help developers and organizations rapidly create intelligent, cutting-edge, market-ready, and responsible applications with out-of-the-box and prebuilt and customizable APIs and models. Example applications include natural language processing for conversations, search, monitoring, translation, speech, vision, and decision-making.

## Use a custom model on your local machine

If you have the ability to train your own model using your own private data with platforms like [TensorFlow](https://www.tensorflow.org/) or [PyTorch](https://pytorch.org/). You can integrate that custom model into your Windows application by running it locally on the device hardware using [ONNX Runtime](https://onnxruntime.ai/) and AI Toolkit for Visual Studio Code.

**[AI Toolkit for Visual Studio Code](./toolkit/index.md)** is a VS Code Extension that enables you to download and run AI models locally, including access to hardware-acceleration for better performance and scale through [DirectML](./directml/dml-get-started.md). The AI Tookit can also help you with:

- Testing models in an intuitive playground or in your application with a REST API.
- Fine-tuning your AI model, both locally or in the cloud (on a virtual machine) to create new skills, improve reliability of responses, set the tone and format of the response.
- Fine-tuning popular small-language models (SLMs), like [Phi-3](https://azure.microsoft.com/blog/introducing-phi-3-redefining-whats-possible-with-slms/) and [Mistral](https://mistral.ai/).
- Deploy your AI feature either to the cloud or with an application that runs on a device.
- Leverage hardware acceleration for better performance with AI features using DirectML. DirectML is a low-level API that enables your Windows device hardware to accelerate the performance of ML models using the device GPU or NPU. Pairing DirectML with the ONNX Runtime is typically the most straightforward way for developers to bring hardware-accelerated AI to their users at scale. Learn more: [DirectML Overview](./directml/dml.md).

You may also want to look into these [model fine-tuning concepts](fine-tuning.md) to adjust a pre-trained model to better fit your data.

### Find open source models

You can find open source ML models on the web, a few of the most popular include:

- [Hugging Face](https://huggingface.co/models): A hub of over 10,000 pre-trained ML models for natural language processing, powered by the Transformers library. You can find models for text classification, question answering, summarization, translation, generation, and more.
- [ONNX Model Zoo](https://onnx.ai/models/): A collection of pre-trained ML models in ONNX format that cover a wide range of domains and tasks, such as computer vision, natural language processing, speech, and more.
- [Qualcomm AI Hub](https://aihub.qualcomm.com/models): A platform that provides access to a variety of ML models and tools optimized for Qualcomm Snapdragon devices. You can find models for image, video, audio, and sensor processing, as well as frameworks, libraries, and SDKs for building and deploying ML applications on mobile devices. Qualcomm AI Hub also offers tutorials, guides, and community support for developers and researchers.
- [Pytorch Hub](https://pytorch.org/hub/): A pre-trained model repository designed to facilitate research reproducibility and enable new research. It is a simple API and workflow that provides the basic building blocks for improving machine learning research reproducibility. PyTorch Hub consists of a pre-trained model repository designed specifically to facilitate research reproducibility.
- [TensorFlow Hub](https://www.tensorflow.org/hub): A repository of pre-trained ML models and reusable components for TensorFlow, which is a popular framework for building and training ML models. You can find models for image, text, video, and audio processing, as well as transfer learning and fine-tuning.
- [Model Zoo](https://modelzoo.co/): A platform that curates and ranks the best open source ML models for various frameworks and tasks. You can browse models by category, framework, license, and rating, and see demos, code, and papers for each model.

Some model libraries are **not intended to be customized** and distributed via an app, but are helpful tools for hands-on exploration and discovery as a part of the development lifecycle, such as:

- [Ollama](https://ollama.com/library): Ollama is a marketplace of ready-to-use ML models for various tasks, such as face detection, sentiment analysis, or speech recognition. You can browse, test, and integrate the models into your app with a few clicks.
- [LM Studio](https://lmstudio.ai/): Lmstudio is a tool that lets you create custom ML models from your own data, using a drag-and-drop interface. You can choose from different ML algorithms, preprocess and visualize your data, and train and evaluate your models.

## Use Responsible AI practices

Whenever you are incorporating AI features in your Windows app, we **highly** recommend following the [Developing Responsible Generative AI Applications and Features on Windows](rai.md) guidance.

This guidance will help you to understand governance policies, practices, and processes, identify risk, recommend testing methods, utilize safety measures like moderators and filters, and calls out specific considerations when selecting a model that is safe and responsible to work with.

Windows Copilot Runtime on-device generative AI models can help you to enforce local content safety features, such as on-device classification engines for harmful content and a default blocklist. Microsoft prioritizes supporting developers to build safe, trustworthy AI experiences with local models on Windows.

## Additional resources

- [Phi Silica, small but mighty on-device SLM](https://blogs.windows.com/windowsexperience/2024/12/06/phi-silica-small-but-mighty-on-device-slm/) (Windows Blogs)

- [AI on Windows Sample Gallery](./samples/index.md): Samples demonstrating ways to integrate AI into Windows apps.

- [FAQs about using AI with Windows](faq.yml): Frequently asked questions about terminology and concepts related to using AI in a Windows context, covering questions like "What is DirectML?", "What is ONNX?", "What is ORT?", "What is an NPU?", "What is an SLM?", "What is inferencing?", "What is fine-tuning?", etc.

- [ONNX](https://onnx.ai/): An open standard for representing and exchanging ML models across different frameworks and platforms. If you find a pre-trained ML model in ONNX format, you can use [ONNX Runtime (ORT)](https://onnxruntime.ai/) to load and run the model in your Windows app. ORT allows you to access the hardware-accelerated inference capabilities of your device and optimize the performance of your ML model. If you have a pre-trained ML model in a different format, such as PyTorch or TensorFlow, you can convert it to ONNX using a model optimization tool like [Olive](https://github.com/microsoft/OLive). For help using Olive, see [Fine-tune SLM with Microsoft Olive (Journey Series for Generative AI Application Architecture)](https://techcommunity.microsoft.com/t5/educator-developer-blog/journey-series-for-generative-ai-application-architecture-fine/ba-p/4080813). For tutorials on creating and using ONNX models, see [ONNX Tutorials on GitHub](https://github.com/onnx/tutorials). For samples demonstrating how to use ONNX models in a Windows app, see the [AI on Windows Sample Gallery](./samples/index.md).

- [Get started with Phi3 and other language models in your Windows app with ONNX Runtime Generative AI](models/get-started-models-genai.md)

- [Get started with ONNX models in your WinUI app with ONNX Runtime](models/get-started-onnx-winui.md)

- [WebNN API for web apps](https://github.com/webmachinelearning/webnn/tree/main): A web standard for accessing neural network hardware acceleration in browsers, based on the WebIDL and JavaScript APIs. It enables web developers to create and run machine learning models efficiently on the client-side, without relying on cloud services or native libraries. [WebNN Samples on GitHub](https://github.com/webmachinelearning/webnn-samples). [WebNN samples using ONNX Runtime in the AI on Windows Sample Gallery](./samples/index.md#local-hardware-acceleration-through-directml).

- [PyTorch](https://pytorch.org/): A very popular open-source deep learning framework available with a Python and C++ interface. This will likely be the most common format your will find for ML models. If you want to use PyTorch ML models in your Windows (C# or C++) app or in a web app, you can use [TorchSharp](https://github.com/dotnet/TorchSharp) and [LibTorch](https://github.com/pytorch/pytorch/blob/main/docs/libtorch.rst), which are .NET and C++ bindings for the PyTorch library. TorchSharp and LibTorch allow you to create, load, and manipulate tensors, build and run neural networks, and save and load models using the PyTorch format. For samples, checkout [TorchSharp Examples](https://github.com/dotnet/TorchSharpExamples), [TorchScript for Deployment](https://pytorch.org/tutorials/recipes/torchscript_inference.html), [PyTorch C++ Examples](https://github.com/pytorch/examples/tree/main/cpp). For web apps, check out [Build a web application with ONNX Runtime](https://onnxruntime.ai/docs/tutorials/web/build-web-app.html). For examples of how to run PyTorch models with DirectML, see the [AI on Windows Sample Gallery](./samples/index.md).

- [TensorFlow](https://www.tensorflow.org/) is another popular open-source software library for machine learning and artificial intelligence used to build and deploy machine learning models for various tasks.
