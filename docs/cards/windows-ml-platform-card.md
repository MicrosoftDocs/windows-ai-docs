---
title: Platform card - Windows ML
description: Learn about Windows ML's features, capabilities, intended uses, and responsible AI considerations.
author: adrastogi
ms.author: adityar
ms.date: 11/02/2026
ms.topic: concept-article
ms.service: windows
---

# Platform card: Windows ML

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how enterprise customers and individuals can engage with AI responsibly.

## Overview

Windows ML is the local AI inference framework for Windows. It lets applications run AI models directly on a Windows PC and use the PC's CPU, GPU, or neural processing unit (NPU). Windows ML is built on ONNX Runtime, so developers can use models exported from PyTorch, TensorFlow, scikit-learn, Hugging Face, and other frameworks in the ONNX format, and keep using the ONNX Runtime APIs they already know.

Windows ML helps applications use hardware acceleration without bundling vendor-specific software. Through its execution provider catalog, Windows can download, install, and update hardware-specific execution providers from AMD, Intel, NVIDIA, and Qualcomm on demand. A model catalog lets applications and libraries download compatible models from online catalogs they choose and share those models across applications on the same device. Running models locally can reduce latency, keep application data on the device, and allow applications to work without an internet connection.

Windows ML does not include or select AI models. Developers choose, license, and distribute the models their applications use. Windows ML is intended for Windows application, framework, and library developers using C#, C++, or Python. Some newer Windows ML capabilities are experimental, as shown in the following table.

| Capability | Status |
|------------|--------|
| ONNX Runtime APIs included in Windows ML | Generally available |
| Execution provider catalog and included CPU and DirectML execution providers | Generally available |
| Downloadable vendor execution providers (AMD MIGraphX and VitisAI, Intel OpenVINO, NVIDIA TensorRT for RTX, Qualcomm QNN) | Generally available |
| Model catalog | Generally available |
| DX CGC backend and WinML CG execution provider for compiled DirectX GPU acceleration | Experimental |
| Windows ML Runtime API, including GGUF models through llama.cpp | Experimental |
| Text Generation, Chat, and Speech Recognition Task APIs, and the local OpenAI-compatible endpoint | Experimental |

### About experimental capabilities

Experimental capabilities are early-stage features released so developers can try them and give feedback. They are **not supported** for production use because:

- **No support or security fixes**: Fixes ship only in later experimental releases, so an application that includes an experimental release keeps its defects.
- **Limited validation**: They have been tested with a limited set of models, devices, and drivers, and may produce incorrect results or fail on others.
- **Breaking changes**: APIs and file formats may change, and a capability may be removed or never become generally available.

Use experimental capabilities only to evaluate, prototype, and provide feedback, on development and test devices with test data. Do not use them in applications distributed to end users, including through the Microsoft Store, with personal or sensitive data, or for decisions that affect people. For production applications, use the generally available capabilities in the preceding table.

## Key terms

The following list provides a glossary of key terms related to Windows ML:

| Term | Definition |
|------|------------|
| **DX CGC (DirectX Compute Graph Compiler) backend** | An experimental Windows ML backend that uses the DirectX Compute Graph Compiler to turn a model into an optimized program for a specific DirectX 12 GPU. It is available through the Windows ML Runtime API and, for existing ONNX Runtime code, through the WinML CG execution provider. |
| **Execution provider** | A component that lets ONNX Runtime run a model on specific hardware, such as an NPU or GPU from a particular vendor. Windows ML includes CPU and DirectML execution providers and can download others on demand. |
| **Execution provider catalog** | A Windows ML API that finds execution providers compatible with the device, downloads and installs them, and registers them for use. It also reports whether Microsoft has certified each provider. |
| **GGUF** | A file format from the llama.cpp ecosystem that packages a language model's weights, tokenizer, and settings in one file. GGUF models are supported only through the experimental Runtime API. |
| **Inference** | Running a trained model on new input to produce an output, such as a classification, image, or generated text. Windows ML performs inference locally on the device. |
| **Local model** | An AI model that runs on the user's device rather than in the cloud. Windows ML runs models that the application supplies. |
| **Model catalog** | A Windows ML API that downloads models from online catalogs the application specifies, selects variants compatible with the device, and shares downloaded models across applications. |
| **NPU (neural processing unit)** | A processor designed to run AI workloads efficiently with lower power use than a CPU or GPU. |
| **ONNX** | An open format for AI models. Windows ML runs ONNX models through ONNX Runtime. |
| **ONNX Runtime** | An open-source engine for running ONNX models. Windows ML includes a Windows-supported version of ONNX Runtime. |
| **Windows ML Runtime API** | An experimental Windows-native API for loading models, choosing hardware, and connecting models into pipelines. It supports ONNX models, compiled models through the DX CGC backend, and, through llama.cpp, GGUF language models. |

## Key features or capabilities

The key features and capabilities outlined here describe what Windows ML is designed to do and how it performs across supported tasks.

- **Local inference with ONNX Runtime**: Applications run ONNX models on the device using the ONNX Runtime APIs included in Windows ML. Existing ONNX Runtime code works without changes, and Windows ML is supported on x64 and Arm64 Windows PCs and Windows Server.
- **Hardware acceleration through managed execution providers**: The execution provider catalog finds execution providers that match the device's hardware, downloads and installs them, and keeps them up to date through Windows. Applications can accelerate models on NPUs and GPUs without bundling vendor software, and ONNX Runtime falls back to the CPU for parts of a model a provider cannot run.
- **Model catalog and shared model storage**: Applications and libraries can download models from catalogs they control, and Windows ML selects the model variant compatible with the device's execution providers. Downloaded files are verified against the catalog's published hash, and applications that request the same model share one copy.
- **Flexible deployment**: Applications can use Windows ML as a shared component through the Windows App SDK, so they receive runtime updates through Windows, or include it with the application. Windows ML is available from C#, C++, and Python.
- **Windows ML Runtime API (experimental)**: The experimental Runtime API gives native and Python developers explicit control over model loading, hardware placement, and multi-step pipelines. It runs ONNX models and, through a pinned version of llama.cpp, GGUF language models on the CPU or on NVIDIA GPUs with optional CUDA packages.
- **Task APIs and local endpoint (experimental)**: The experimental Text Generation, Chat, and Speech Recognition APIs simplify common workflows over a model the application supplies. A local OpenAI-compatible endpoint accepts chat requests from apps on the same device, and Text Generation can optionally add a statistical watermark to generated text.
- **Compiled DirectX GPU acceleration (experimental)**: The experimental DX CGC backend uses the DirectX compiler to turn ONNX models into programs for a specific GPU. Developers can try it through the Runtime API or, with existing ONNX Runtime code, through the WinML CG execution provider, and can compile models ahead of time. It works with DirectX 12 GPUs from all hardware vendors; performance depends on driver support.

## Intended uses

Windows ML can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

- **Adding on-device AI features to applications**: Developers can add features such as image classification, object detection, background removal, audio processing, or text analysis using ONNX models they choose. Running locally can provide fast, predictable responses and keep user content on the device. The application remains responsible for model quality and for how results are used.
- **Accelerating existing ONNX workloads on Windows hardware**: Teams with ONNX models can use the execution provider catalog to run them on NPUs and GPUs across Windows PCs without building separate versions for each vendor. This can improve performance and battery life while keeping one code path. Teams should validate accuracy and performance on each hardware type they support.
- **Distributing models for libraries and frameworks**: Library and SDK authors can use the model catalog to download large models when needed instead of shipping them with every application. Applications that use the same model share one verified copy on the device, which saves storage and bandwidth.
- **Supporting privacy-sensitive and offline scenarios**: Organizations in fields such as education, healthcare, and the public sector can process data on the device when policies discourage sending it to cloud services. Local processing does not by itself make an application compliant, and these scenarios require domain-specific validation and human oversight.
- **Evaluating open-source language models (experimental)**: Developers can use the experimental Runtime and Task APIs to try GGUF and ONNX language models locally, prototype text and speech features, and provide feedback. These capabilities are for evaluation on development and test devices, not for applications distributed to end users.
- **Evaluating compiled GPU performance (experimental)**: Developers can use the experimental DX CGC backend on development and test devices to compare GPU performance against their existing ONNX Runtime setup. This helps teams provide feedback and decide whether DX CGC would benefit their models if it becomes generally available.

## Models and training data

Windows ML is a framework for running models; it does not include, train, or select AI models. Applications choose, license, and distribute their own models, or download them from catalogs they specify, so model behavior, training data, language coverage, and safety tuning depend on the selected model and its publisher. Developers should review the model card and data documentation for each model they use. Windows ML samples use public models such as [ResNet-50](https://huggingface.co/microsoft/resnet-50) for image classification and, for the experimental text generation APIs, [Qwen2.5-0.5B-Instruct](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct). These are examples, not models supplied or endorsed for every application. For Windows AI features that include Microsoft-provided models, see [Windows AI APIs](/windows/ai/apis/).

## Performance

Windows ML is expected to perform reliably when an application runs a valid ONNX model that the selected execution provider supports, on a device with up-to-date drivers and enough memory for the model. The CPU and DirectML execution providers are available on all supported Windows versions. Downloadable NPU and GPU execution providers require Windows 11 version 24H2 or later and compatible hardware and drivers; each provider's requirements are listed in [Windows ML execution providers](../new-windows-ml/supported-execution-providers.md).

Windows ML supports any input and output types the application's ONNX model defines, such as images, audio, text tokens, and numeric data. Windows ML itself does not interpret inputs or outputs, so performance and accuracy depend on the model and how the application prepares data and uses results.

Results can differ slightly across execution providers and devices because hardware may use different numeric precision, compressed (quantized) models, or provider-specific optimizations. Speed also depends on the model, the hardware, and whether all parts of a model run on the accelerator or some parts fall back to the CPU. Developers should measure accuracy and performance on each hardware type they plan to support.

For the experimental llama.cpp backend, GGUF models run on the CPU on x64 and Arm64 devices or on compatible NVIDIA GPUs with optional CUDA packages; NPUs are not supported. Windows ML does not define supported natural languages; language coverage comes from the selected model, and experimental language-model validation has focused primarily on English.

The experimental DX CGC backend works with DirectX 12 GPUs from all hardware vendors. On GPUs whose drivers do not yet include optimized DX CGC support, models run through a general-purpose GPU path, which may be slower. A compiled model is specific to the GPU, driver, and compiler version it was compiled for, so it may need to be recompiled after driver or Windows ML updates. Not every ONNX model can be compiled; models that use unsupported operations or have shapes that change at run time may fail to compile and should use ONNX Runtime instead.

## Limitations

Understanding Windows ML's limitations is crucial to determine if it is used within safe and effective boundaries. While we encourage customers to leverage Windows ML in their innovative solutions or applications, it's important to note that Windows ML was not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals) as well as the following considerations when choosing a use case:

- **Model-dependent quality and safety**: Windows ML runs the model the application supplies and does not determine whether it is accurate, fair, safe, or appropriate. Model outputs may be incorrect, biased, or harmful, and generative models may fabricate information. Developers must evaluate the exact model, version, and settings they use for their scenario.
- **No built-in content moderation**: Windows ML does not detect or block harmful inputs or outputs such as hate, sexual content, violence, self-harm, or jailbreak attempts. Applications that generate or display content must add safeguards appropriate to their scenario.
- **Results vary across hardware**: The same model can produce slightly different results, and run at different speeds, on different execution providers and devices. Applications whose decisions depend on exact scores or thresholds should validate behavior on every supported hardware type.
- **Hardware, driver, and OS requirements**: Downloadable execution providers require Windows 11 version 24H2 or later and supported hardware and drivers. On other devices, applications run on the CPU or DirectML, which may be slower, so applications should be designed to work acceptably on fallback hardware.
- **Third-party execution providers and catalogs**: Vendor execution providers are developed by hardware partners under their own license terms, and model catalogs are operated by the application or a third party. The execution provider catalog reports whether Microsoft has certified each provider; uncertified providers and third-party catalogs require the developer's own review.
- **Experimental capabilities**: The Runtime API, DX CGC backend and WinML CG execution provider, llama.cpp backend, Task APIs, and local endpoint are experimental and not supported for production use; see [About experimental capabilities](#about-experimental-capabilities). They have been validated only with selected models and devices, including mostly small language models and, for the DX CGC backend, selected GPUs and drivers.

## Evaluations

Performance and safety evaluations assess whether AI applications are operating reliably and securely by examining factors like groundedness, relevance, and coherence while identifying the risks of generating harmful content. The following evaluations were conducted with safety components already in place, which are also described in [Safety components and mitigations](#safety-components-and-mitigations).

Microsoft Foundry automated quality and harmful-content evaluations were not conducted for Windows ML because it does not supply or select a model. The following custom evaluations assess the platform itself.

### Custom evaluations

Execution provider evaluations check that vendor execution providers install, register, and run representative models correctly on their target hardware. Providers that meet Microsoft's quality and compatibility requirements are reported as certified by the execution provider catalog. An ideal result is correct output and stable performance across supported devices; installation failures, incorrect results, or crashes are suboptimal.

Functional and compatibility evaluations test model loading and execution through ONNX Runtime, execution provider discovery and installation, model catalog downloads and hash verification, deployment as a shared or bundled component, and the C#, C++, and Python APIs. For the experimental Runtime and Task APIs, evaluations also cover text generation, streaming, cancellation, chat formatting, output constraints, watermark detection, and local endpoint access controls. For the experimental DX CGC backend, evaluations compare outputs and GPU execution time against Microsoft's native DirectX compiler tools on lab GPUs, and check that compiled models are rejected on a mismatched device or configuration.

Security evaluations test handling of malformed models and catalog files, network and file-path validation, component integrity checks, and local endpoint access controls. These evaluations measure platform robustness; they do not measure whether a model an application selects produces harmful or unfair results.

## Safety components and mitigations

Windows ML includes safeguards for local processing, component integrity, model download integrity, and experimental local endpoints. These safeguards reduce platform risks but do not replace model-specific responsible AI evaluation or application content-safety controls.

**Local processing and limited diagnostic data**

Models run in the application's process on the local device. Windows ML diagnostic data records operation results, such as error codes, execution provider names and installation states, and model catalog request identifiers, along with the Windows ML version. It does not record model inputs, model outputs, or model contents.

**Managed and certified execution providers**

Windows installs and updates execution providers as signed packages, so applications do not need to bundle vendor software. The execution provider catalog reports each provider's certification state and provides methods that register only certified providers.

**Verified model downloads**

Model catalog downloads are checked against the SHA-256 hash published in the catalog before applications can use them, and package-based models must have a trusted code signature. Applications choose which catalogs to trust.

**Safeguards for experimental capabilities**

The experimental DX CGC backend checks that a compiled model matches the device and configuration it runs on. The experimental llama.cpp backend verifies its components before loading them and rejects models stored on network locations. The experimental local endpoint accepts connections only from the same device, requires a per-session access key, and rejects requests from web browsers.

**Application-owned content safeguards**

Developers should add safeguards appropriate to their model and scenario, such as input and output filtering, output constraints, grounding, rate limits, and user feedback paths. Safeguards should be tested with representative and adversarial data for every intended language and user population.

## Best practices for deploying and adopting Windows ML

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications and platform services with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

**Deployers and end-users should:**

**Exercise caution and evaluate outcomes when using Windows ML for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains such as financial platforms, healthcare, and housing require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

**Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

**End-users should:**

**Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by applications built on Windows ML and verify that they match their expectations and requirements.

**Be aware of the risk of overreliance**: Overreliance on AI happens when users accept incorrect or incomplete AI outputs, mainly because mistakes in AI outputs may be hard to detect. For the end-user, overreliance could result in decreased productivity, loss of trust, application abandonment, financial loss, psychological harm, physical harm, among others (e.g., a doctor accepts an incorrect AI output).

**Exercise caution when designing agentic AI in sensitive domains**: Users should exercise caution when designing and/or deploying agentic AI applications in sensitive domains where agent actions are irreversible or highly consequential. Additional precautions should also be taken when creating autonomous agentic AI as described further in either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals).

**Understand how the application handles data**: Running a model locally does not guarantee that an application keeps all data on the device. Review the application's privacy information, and report unexpected or harmful behavior through the application's feedback channel.

**Deployers should:**

- **Choose and document models carefully**: Select models whose license, model card, and training data documentation fit the intended use. Record each model's source and version, and revalidate after updating, converting, or compressing a model.
- **Test on the hardware your users have**: Validate accuracy and performance on each execution provider and device type you support, including CPU fallback. Confirm that results remain acceptable when the same model runs on different hardware.
- **Review execution provider and catalog trust**: Prefer certified execution providers, and review vendor license terms before use. Use only model catalogs you control or trust, and publish SHA-256 hashes for every model file in catalogs you operate.
- **Add safeguards for generated content**: If your application generates or displays text, images, or other content, add filtering and review appropriate to your scenario. Test with adversarial inputs and with data that represents your users' languages and populations.
- **Use experimental capabilities only for evaluation**: Use the Runtime API, DX CGC backend and WinML CG execution provider, llama.cpp backend, Task APIs, and local endpoint only to evaluate, prototype, and provide feedback, not in applications distributed to end users. Report issues and feedback through [Windows ML GitHub issues](https://github.com/microsoft/WindowsML/issues).
- **Monitor quality after deployment**: Track errors, performance, and user-reported issues after release. Retest after Windows, driver, execution provider, Windows ML, or model updates, because any of these can change behavior.

## Learn more about Windows ML

For additional guidance or to learn more about the responsible use of Windows ML, we recommend reviewing the following documentation:

- [What is Windows ML?](../new-windows-ml/overview.md)
- [Windows ML execution providers](../new-windows-ml/supported-execution-providers.md)
- [Share models across apps with the Windows ML Model Catalog APIs](../new-windows-ml/model-catalog/overview.md)
- [Windows ML Runtime API overview (experimental)](../new-windows-ml/runtime/overview.md)
- [Windows ML third-party notices](../new-windows-ml/third-party-notices.md)
- [Responsible Generative AI Development on Windows](../rai.md)
- [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement)
- [Report a security vulnerability to the Microsoft Security Response Center](https://msrc.microsoft.com/create-report)

**Learn more about responsible AI**

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/azure/machine-learning/concept-responsible-ai)
