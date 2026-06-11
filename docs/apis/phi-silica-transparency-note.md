---
title: "Transparency Note: Phi Silica on Non-Copilot+ PCs"
description: Learn about the capabilities, limitations, and responsible use of Phi Silica on non-Copilot+ PCs with GPU inference.
ms.topic: concept-article
ms.date: 05/26/2026
---

# Transparency Note: Phi Silica on Non-Copilot+ PCs

## What is a Transparency Note?

An AI system includes not only the technology, but also the people who will use it, the people who will be affected by it, and the environment in which it is deployed. Creating a system that is fit for its intended purpose requires an understanding of how the technology works, its capabilities and limitations, and how to achieve the best performance.

Microsoft provides *transparency notes* to help you understand how our AI technology works. This includes the choices system owners can make that influence system performance and behavior, and the importance of thinking about the whole system, including the technology, the people, and the environment. You can use transparency notes when developing or deploying your own system, or share them with the people who will use or be affected by your system.

Transparency notes are part of a broader effort at Microsoft to put our [AI principles](https://www.microsoft.com/ai/responsible-ai) into practice.

---

## Introduction to Phi Silica

Phi Silica is a small language model (SLM) tuned for on-device inference on Windows. It enables local generative AI capabilities — including text generation, summarization, rewriting, and text-to-table transformation — without requiring a cloud connection. By running entirely on-device, Phi Silica supports user privacy by keeping prompts and responses local.

Phi Silica was originally designed and optimized exclusively for **Windows Copilot+ PCs**, which include a Neural Processing Unit (NPU) providing 40+ TOPS of dedicated AI compute. With this expansion, Phi Silica is now available on **non-Copilot+ PCs** — standard Windows devices that lack a dedicated NPU — where inference is performed using the device's GPU.

### Key Terms

| Term | Definition |
|------|-----------|
| **Copilot+ PC** | A Windows PC with a dedicated Neural Processing Unit (NPU) meeting the 40+ TOPS threshold, purpose-built for accelerated on-device AI workloads. |
| **Non-Copilot+ PC** | A standard Windows PC without a dedicated NPU (or with an NPU below the Copilot+ threshold). Phi Silica inference on these devices is performed via the GPU. |
| **NPU (Neural Processing Unit)** | Specialized hardware designed to accelerate machine learning workloads with high efficiency and low power consumption. |
| **SLM (Small Language Model)** | A language model optimized for local, on-device execution with a smaller parameter count than cloud-scale large language models (LLMs). |
| **Speculative Decoding** | A technique that uses a smaller draft model to propose multiple token sequences, which are then validated in parallel by the main model to accelerate text generation. |
| **Content Moderation** | Filtering mechanisms that detect and block harmful, offensive, or unsafe content in model inputs and outputs. |

---

## Capabilities

### System Behavior

Phi Silica processes natural language prompts on-device and generates text responses. The model supports:

- **Free-form text generation**: Answering questions, providing explanations, and generating creative content from user prompts.
- **Text Intelligence Skills**: Built-in transformation capabilities including summarization, rewriting (with tone adjustment), and text-to-table formatting.
- **Streaming responses**: Partial results returned progressively for responsive user experiences.
- **Content moderation integration**: Configurable content filtering across four categories (violence, hate, sexual content, self-harm) at multiple severity levels.

On Copilot+ PCs, Phi Silica leverages NPU hardware for optimized inference with lower latency and reduced power consumption. **On non-Copilot+ PCs, inference runs on the GPU.** The underlying model and its capabilities remain the same; however, operational characteristics differ (see [Limitations](#limitations)).

### Use Cases

#### Intended Uses

- **In-app writing assistance**: Summarizing documents, rewriting text for clarity or tone, and generating drafts within productivity applications.
- **Local Q&A and information retrieval**: Answering factual questions using the model's training data, without transmitting user queries to a cloud service.
- **Accessibility features**: Simplifying complex text, generating descriptions, and supporting users who benefit from AI-assisted reading and writing.
- **Developer prototyping**: Enabling application developers to integrate local AI text generation for testing and proof-of-concept scenarios.
- **Privacy-sensitive workflows**: Scenarios where data sovereignty, offline operation, or user privacy requirements preclude the use of cloud-based AI services.

#### Considerations When Choosing Use Cases

- **Do not use for high-stakes decision-making without human oversight.** Phi Silica, like all language models, can produce inaccurate, incomplete, or fabricated information (hallucinations). Applications that inform medical, legal, financial, or safety-critical decisions must include meaningful human review.
- **Do not use as a sole source of factual truth.** The model's training data has a knowledge cutoff and may contain biases or inaccuracies. Encourage users to verify important information from authoritative sources.
- **Be cautious with sensitive personal data.** While on-device processing enhances privacy, developers should avoid designing workflows that prompt the model with sensitive personal information (e.g., health records, financial details) unless appropriate safeguards are in place.
- **Consider the expanded user base.** Expansion to non-Copilot+ PCs significantly broadens the user population, including devices with varying hardware capabilities and users with diverse technical literacy. Design your application to gracefully handle performance variance and provide clear expectations to users.

---

## Limitations

### Technical Limitations Specific to Non-Copilot+ PCs

| Factor | Copilot+ PCs (NPU) | Non-Copilot+ PCs (GPU) |
|--------|--------------------|-----------------------------|
| **Inference Latency** | Optimized; low latency via NPU acceleration and speculative decoding | Higher latency; inference speed depends on GPU generation, available VRAM, and current GPU load |
| **Power Consumption** | NPU is power-efficient, suitable for battery-powered use | GPU inference consumes more power; may significantly impact battery life on laptops |
| **Concurrent Workloads** | NPU operates independently, minimizing impact on other tasks | GPU inference competes with other GPU-accelerated applications (rendering, video playback, gaming) for compute resources; may cause system slowdowns |
| **Thermal Impact** | NPU designed for sustained AI workloads with managed thermals | Extended GPU inference may cause thermal throttling on devices not designed for sustained GPU compute loads |
| **Memory Pressure** | Model loading and inference managed alongside NPU memory | Model must be loaded into GPU VRAM (or shared GPU memory); devices with limited VRAM may experience pressure or fall back to slower shared memory |
| **Prompt Compression** | ✅ Available; enables longer effective context windows | ❌ Not available on GPU |
| **Speculative Decoding** | ✅ Available; accelerates text generation using a draft model | ❌ Not available on GPU |
| **Model Optionality** | Model is managed by the system | Model is downloaded on demand and can be removed by the user via **Settings** > **System** > **AI Components** |

### General Technical Limitations

- **Accuracy and hallucinations**: Phi Silica may generate plausible-sounding but factually incorrect responses. The model's output quality does not change between NPU and GPU execution, but the reduced responsiveness on non-Copilot+ PCs may lead users to accept initial outputs without critical review.
- **Language support**: Phi Silica's language capabilities are determined by its training data. Performance may vary across languages, and some languages may not be supported. Phi Silica features are not available in China.
- **Context window limitations**: The model has a fixed context window. Long prompts or multi-turn conversations may exceed this window, leading to loss of earlier context.
- **Bias and fairness**: The model may reflect biases present in its training data, including stereotypes related to gender, race, ethnicity, religion, or other characteristics. Developers should evaluate outputs for fairness across the populations their application serves.
- **Adversarial inputs**: The model may be susceptible to prompt injection or jailbreak attempts. Content moderation filters mitigate but do not eliminate this risk.

### Operational Factors for Non-Copilot+ PCs

- **Hardware diversity**: Non-Copilot+ PCs span a wide range of GPU configurations (integrated vs. discrete GPUs, varying VRAM capacities, different GPU architectures). Performance will vary significantly across this device spectrum.
- **Minimum hardware requirements**: Devices must meet minimum GPU and memory requirements to run Phi Silica. Devices with only integrated graphics or older discrete GPUs may experience degraded performance or may not meet minimum thresholds.
- **Background resource contention**: Unlike NPU-based execution, GPU inference is affected by other GPU-accelerated workloads. Users running graphics-intensive applications (gaming, video editing, 3D rendering) simultaneously may experience poor model performance.
- **Software dependencies**: Non-Copilot+ PC execution requires the latest IHV GPU driver installed directly from the GPU manufacturer (currently NVIDIA only; AMD support coming soon). Default drivers from Windows Update or OEM installations may not be sufficient and can cause failures or degraded performance.

---

## System Performance

### Understanding Performance on Non-Copilot+ PCs

Phi Silica's output quality (accuracy, coherence, relevance) is consistent across Copilot+ and non-Copilot+ PCs because the same model weights and architecture are used. The primary differences are in **inference speed**, **resource consumption**, and **user experience responsiveness**.

Developers should:

- **Benchmark on representative hardware**: Test on a range of non-Copilot+ devices that reflect your target user base, including lower-end configurations.
- **Set user expectations**: Clearly communicate that response times may vary based on device hardware. Consider showing progress indicators or streaming partial results.
- **Implement timeouts and fallbacks**: For scenarios where response time is critical, implement appropriate timeouts and consider offering cloud-based fallback options (with user consent).
- **Monitor resource usage**: Track GPU utilization, VRAM consumption, and system memory usage during inference to identify and address performance bottlenecks.

### Content Moderation

Content moderation is available and strongly recommended on both Copilot+ and non-Copilot+ PCs. The `ContentFilterOptions` API allows developers to configure severity thresholds for violence, hate, sexual content, and self-harm categories. Content moderation behavior is identical regardless of the inference hardware.

**Best practices:**
- Always enable content moderation for user-facing applications.
- Configure severity levels appropriate to your user population (e.g., stricter settings for applications used by minors).
- Do not rely solely on automated content moderation; include mechanisms for user reporting and human review of flagged content.

---

## Evaluation and Integration Guidance

### Before Deployment

1. **Conduct end-to-end testing** on non-Copilot+ hardware representative of your target users, including:
   - Functional testing of model outputs across your application's use cases.
   - Performance testing under realistic resource contention scenarios.
   - Red teaming to identify potential misuse patterns, especially those that may exploit slower inference (e.g., time-based side-channel risks).

2. **Evaluate fairness and bias** across the user populations your application serves. Test with prompts in all supported languages and representing diverse demographics.

3. **Assess user experience** on lower-end devices. Ensure that degraded performance does not lead to user frustration, abandonment, or over-reliance on incomplete outputs.

### After Deployment

1. **Monitor telemetry** (in compliance with applicable privacy laws and policies) for:
   - Inference latency distributions across device types.
   - Content moderation trigger rates.
   - User feedback signals indicating quality or performance issues.

2. **Maintain an incident response plan** that includes the ability to disable or update the model if a safety issue is discovered.

3. **Iterate on content moderation** settings based on real-world usage patterns, user feedback, and emerging content safety threats.

4. **Provide user feedback mechanisms** directly in the application experience, allowing users to report inaccurate, harmful, or unexpected outputs.

---

## Responsible AI Considerations for the Non-Copilot+ Expansion

### Broader Reach, Broader Responsibility

Expanding Phi Silica to non-Copilot+ PCs makes local AI capabilities accessible to a much larger and more diverse user population. This increased reach amplifies both the benefits and the risks:

- **Democratized access**: More users can benefit from on-device AI without purchasing specialized hardware. This supports inclusiveness and equity.
- **Increased misuse surface**: A larger user base increases the statistical likelihood of adversarial or harmful use. Robust content moderation and abuse monitoring are essential.
- **Diverse device contexts**: Users on non-Copilot+ PCs may be in environments with limited connectivity, older hardware, or shared devices — all of which affect how the AI is experienced and potentially misused.

### Privacy

Phi Silica processes all prompts and generates all responses locally on the user's device. No user prompts or model outputs are transmitted to Microsoft or any third party during inference. This privacy architecture is preserved on non-Copilot+ PCs.

Developers integrating Phi Silica should:
- Clearly disclose to users that AI processing occurs on-device.
- Avoid collecting or logging prompts and responses unless the user has provided informed consent.
- Follow applicable privacy laws, regulations, and Microsoft privacy policies regarding any telemetry or diagnostic data collected.

### Transparency

- Disclose to users when content is generated by AI. Do not present AI-generated text as human-authored.
- Communicate that the AI may produce errors and encourage users to verify important information.
- On non-Copilot+ PCs, inform users that performance may differ from that on Copilot+ hardware.

### Fairness

- Evaluate model outputs for potential biases across languages, cultures, and demographic groups.
- Monitor for disparate performance or quality across different hardware configurations to ensure equitable user experiences.

### Reliability and Safety

- Implement content moderation with severity levels appropriate for your user population.
- Design your application to handle model failures gracefully (e.g., model unavailability, out-of-memory conditions on constrained devices).
- Provide a kill switch or feature flag to disable Phi Silica functionality rapidly if a safety issue is identified.

### Accountability

- Maintain documentation of your AI integration decisions, content moderation configurations, and evaluation results.
- Designate a responsible individual or team accountable for monitoring and responding to AI-related incidents.

---

## Tools and Resources

- [Phi Silica Overview](phi-silica.md) — Main documentation for Phi Silica APIs.
- [Responsible Generative AI Development on Windows](/windows/ai/rai) — Guidelines for govern, map, measure, and manage practices.
- [Microsoft Responsible AI Standard](https://www.microsoft.com/ai/responsible-ai) — Core principles and approach.
- [Content Moderation with Windows AI APIs](content-moderation.md) — Configuring content filters for Phi Silica.
- [AI Dev Gallery](https://github.com/microsoft/ai-dev-gallery/) — Samples and demonstrations.

---

*This transparency note supplements the existing Phi Silica documentation and the [Responsible Generative AI Development on Windows](/windows/ai/rai) guidance. It will be updated as the technology, usage patterns, and mitigations evolve.*
