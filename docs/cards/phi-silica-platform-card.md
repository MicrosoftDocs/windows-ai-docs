---
title: Platform card - Phi Silica
description: Learn about Phi Silica's features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 03/24/2026
ms.topic: concept-article
ms.service: windows
---

# Platform card: Microsoft Foundry on Windows – Phi Silica (Language Model)

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how enterprise customers and individuals can engage with AI responsibly.

## Overview

Phi Silica is a small, efficient language model designed to run directly on a Windows PC's Neural Processing Unit (NPU). Although it is lightweight, it still provides many of the same text-generation capabilities people expect from larger AI models. Apps – whether for consumers or enterprises – can use Phi Silica to generate text locally on the device, which improves speed, privacy, and reliability. To make text generation faster, Phi Silica uses a method called "speculative decoding," where a smaller helper model suggests possible words or phrases, and the main model quickly checks and confirms them. The model is optimized specifically for Windows Copilot+ PCs to deliver an on-device experience that reduces power usage compared to models deployed via the cloud.

Developers can integrate Phi Silica into their apps using the Windows AI APIs in the Windows App SDK. These APIs allow an app to load the model, submit a text prompt, and receive generated responses – all running locally on the user's machine. This tuning and level of optimization are unique to this version of Phi.

## Key terms

The following list provides a glossary of key terms related to Phi Silica:

| Term | Definition |
|------|------------|
| **AI Toolkit (for Visual Studio Code)** | A developer extension for Visual Studio Code that helps you train, fine-tune, and test AI models such as Phi Silica. It includes tools for dataset setup, job submission, and model evaluation. |
| **Content Filter** | A safety feature that limits or blocks types of generated text (for example, violent or harmful content). Developers can set the allowed severity levels when using Phi Silica via Windows AI APIs. |
| **Inference** | The stage where a trained model generates outputs—such as text based on new prompts. Inference happens locally on the device for Phi Silica and can incorporate fine-tuned adapters like LoRA. |
| **Language Model** | An AI system that predicts and generates text. Phi Silica is Microsoft's NPU-optimized local language model for Windows devices, accessible through Windows AI APIs. |
| **Limited Access Feature (LAF)** | A Windows feature category requiring special authorization or a token before developers can use certain APIs (such as Phi Silica APIs). |
| **Local Model** | An AI model that runs directly on the user's device rather than in the cloud. Phi Silica operates entirely on-device for speed, privacy, and reliability. |
| **LoRA (Low-Rank Adaptation)** | A lightweight method for fine-tuning AI models without retraining the entire model. Developers train a small "adapter" that adjusts model behavior for specific scenarios. |
| **NPU (Neural Processing Unit)** | A specialized chip designed to speed up AI workloads while using less power than a CPU or GPU. Phi Silica is optimized to run efficiently on Windows devices with NPUs. |
| **Prompt** | The input text or question that you send to a language model to generate a response. Prompts can be used for tasks like summarizing, rewriting, or general Q&A. |
| **Speculative Decoding** | A technique that speeds up text generation by using a smaller "draft model" to predict possible outputs. The main model then validates those predictions in parallel. |
| **Text Intelligence Skills** | Built-in language features that allow Phi Silica to summarize, rewrite, or format text into tables using predefined skill classes in the Windows AI APIs. |
| **Windows AI APIs** | A set of Windows developer interfaces that provide access to on-device AI models like Phi Silica for text generation, image scaling, and other tasks. |
| **Windows Copilot+ PCs** | A class of Windows devices equipped with next-generation NPUs and system optimizations that enable high-performance local AI workloads. Phi Silica is tuned specifically for these PCs. |
| **Windows App SDK** | A development framework that provides modern Windows APIs - including Windows AI APIs - for building Windows applications. It is the primary integration path for Phi Silica. |

## Key features or capabilities

The key features and capabilities outlined here describe what Phi Silica is designed to do and how it performs across supported tasks.

**Text Generation**

Phi Silica provides a compact, efficient language model capable of generating coherent text directly on supported Windows devices. This generation happens locally via the Windows AI APIs, allowing applications to produce text outputs in response to user-supplied prompts. The model is optimized to ensure responsive behavior on devices that support accelerated on-device AI.

**Summarize (Text Intelligence Skill)**

The model includes a built-in summarization capability that condenses longer text into a shorter, clear summary. This feature returns structured outputs designed for readability and consistency. It is provided as part of the Text Intelligence Skills set exposed through the Windows AI APIs.

**Rewrite (Text Intelligence Skill)**

Phi Silica can rewrite input text to improve clarity, readability, or tone. This includes rephrasing sentences while preserving meaning and applying style adjustments when requested. As with other skills, Rewrite produces standardized outputs that follow predefined formatting rules.

**Text-to-Table (Text Intelligence Skill)**

The model can convert certain types of information into a structured table format. This skill interprets the prompt and organizes relevant content into rows and columns, improving readability when the input contains list-like or categorical information. Formatting follows a predictable structure usable by downstream UI or logic.

**LoRA Fine-Tuning Support**

Phi Silica supports Low-Rank Adaptation (LoRA), allowing developers to load fine-tuned adapter modules that modify the behavior of the base model. This enables customization while keeping the core model lightweight and avoiding full retraining. LoRA adapters integrate with the Windows AI APIs for local execution.

**Windows AI API Integration**

All Phi Silica capabilities—including text generation and Text Intelligence Skills—are accessed through the Windows AI APIs within the Windows App SDK. These APIs allow applications to load the model, provide prompts, run skills, and retrieve formatted outputs, ensuring a consistent developer experience across supported devices.

## Intended uses

Phi Silica can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

**Creating Clear, Polished Text**

Phi Silica can help users turn rough or incomplete writing into clear, well-structured text. You can provide a sentence or paragraph, and the model rewrites it to improve readability, adjust tone, or remove awkward phrasing. This allows end-users to quickly refine content without needing specialized writing skills.

**Summarizing Long or Dense Information**

When users need to understand a long block of text, Phi Silica can produce a concise summary that highlights key points. This is helpful when reading reports, documentation, or lengthy messages, giving users a fast way to grasp essential information. The summary is generated locally, so results appear quickly and work even without internet access.

**Structuring Information Into Tables**

The model can interpret certain types of input and convert them into an organized table format. This is useful when users want to turn a list, description, or multi-item text into something that's easier to scan or compare. The table output follows a predictable structure so it can be used directly in documents, apps, or UI surfaces.

**Generating Text Based on Prompts**

Users can ask Phi Silica questions or provide a text prompt, and the model will generate a coherent response. This can support a range of writing tasks—from drafting short explanations to producing more detailed text—while keeping everything running on the device for speed and privacy.

**Using Custom Behavior Through LoRA Fine-Tuning**

For organizations or developers that load LoRA adapters, Phi Silica can adapt to a specific domain or style. This allows the model to follow specialized terminology or patterns when generating text while still running efficiently on local hardware. LoRA is supported through experimental APIs but behaves predictably once loaded.

## Models and training data

This model is developed and trained by Microsoft in accordance with Microsoft's Responsible AI standards. For more information on Microsoft's approach to Responsible AI development, please refer to this link: [Responsible Generative AI Development on Windows](/windows/ai/responsible-ai).

## Performance

Phi Silica is designed to perform reliably when working with text-only inputs that are written clearly, follow standard grammar, and fall within the model's supported context length. Because all processing happens locally on the device, the model's responses remain consistent and stable across sessions, regardless of network conditions. This local execution also helps ensure predictable latency and reduces variability that might occur in cloud-based generation.

The model outputs text-based responses, including free-form generated text, rewritten content, summarized content, or structured tables through its Text Intelligence Skills. Its performance is strongest when prompts provide enough detail and structure for the model to interpret meaning accurately—for example, when summarizing well-organized paragraphs or rewriting text that contains clear ideas. Vague, fragmented, or highly technical language may reduce output quality if it falls outside the patterns represented in the data used to train the underlying Phi model family.

Phi Silica is optimized to operate within typical device-level constraints, including available memory and active applications. Under sustained system memory pressure, Windows may temporarily unload the model to maintain system stability, which can increase the time to first token the next time an application loads it.

Performance can vary slightly between streaming and non-streaming (synchronous) generation modes. Synchronous generation generally reaches higher token throughput because the model processes larger chunks without incremental moderation, while streaming applies safety checks more frequently, which introduces modest overhead.

## Limitations

Understanding Phi Silica's limitations is crucial to determine it is used within safe and effective boundaries. While we encourage customers to leverage Phi Silica in their innovative solutions or applications, it's important to note that this model was not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals) as well as the following considerations when choosing a use case:

- **Limited context window for long or multi-turn inputs**: Phi Silica supports a relatively small token window – approximately 3.5K tokens – which constrains how much text it can process at once. This may affect scenarios involving long documents, multi-turn chat, or large sized knowledge retrieval when using Retrieval-Augmented Generation (RAG), which require the model to handle larger amounts of context. When inputs exceed the token limit, the model may truncate content or generate incomplete responses. Users should plan use cases around shorter prompts and avoid workloads that depend on extended context.
- **Performance with long prompts**: As prompt length increases, the model requires more computation before producing output, leading to slower responses. Users should always test and evaluate the performance of the model, for any performance-sensitive scenarios that depend on very fast, large-context generation.
- **Content moderation sensitivity**: Phi Silica includes built-in content moderation designed to identify and filter text that may contain sensitive or harmful material. Because this system evaluates both the input and the generated output, it may occasionally flag or block content when the text resembles topics associated with sensitive categories such as hate, violence, sexual content, or self-harm. This behavior is expected, as the model follows predefined safety rules to prevent unintended harmful outputs. Developers can adjust the moderation "severity levels" through the Windows AI APIs to better match their scenario, and they should review outputs carefully to detect whether any over-blocking occurs during evaluation. Tuning these settings helps ensure the model behaves appropriately for the intended use while staying within safety expectations.
- **Hardware and platform considerations**: Phi Silica is designed and optimized to run on Windows Copilot+ PCs equipped with modern Neural Processing Units (NPUs). These NPUs are specialized hardware components that make on-device AI workloads faster and more power-efficient, which allows the model to generate text smoothly and with lower latency. On unsupported devices – such as PCs without an NPU or PCs outside the Copilot+ hardware family – the model may not be available. Developers should ensure that their applications run on supported Windows systems and expect the best results when Phi Silica operates on devices with NPU acceleration.

## Evaluations

Phi Silica does not have publicly released evaluation metrics, but the model is assessed to ensure it works reliably across its supported capabilities—text generation, summarization, rewriting, and text-to-table formatting. These evaluations focus on whether the model produces consistent, coherent outputs, follows prompt instructions, and maintains predictable formatting for each Text Intelligence Skill.

Safety behavior is also reviewed by checking how the model responds to content that may include sensitive or harmful themes. This includes verifying that content-filtering settings behave as expected and allowing developers to tune moderation severity levels to meet their scenario needs.

Finally, the model undergoes performance checks on supported Windows hardware - on Copilot+ PCs with NPUs - to confirm that it loads successfully, responds with stable latency, and performs within expected ranges for local execution.

## Safety components & mitigations

**Built-in content moderation controls**

Phi Silica uses the Windows AI APIs' built-in content moderation system to identify and filter text involving sensitive themes such as hate, sexual content, violence, or self-harm. This helps reduce the risk of generating harmful or inappropriate responses. Developers can configure the moderation "severity levels" using the Windows AI API options to determine how strict or permissive the filtering should be for both the input prompt and the generated output. Adjusting these levels allows teams to balance safety needs with usability and detect whether the model is overly blocking certain content.

**Configurable filtering for different harm categories**

The content moderation system aligns with the harm categories defined by Azure AI Content Safety, ensuring a consistent safety framework across products. Each category—such as Hate, Sexual, Violence, and Self-Harm—can be tuned independently so developers can tailor the experience to their application's context. This approach helps reduce unintended triggering of filters while still enforcing clear boundaries around high-risk content.

**Protection against high-risk content**

By default, the Windows AI APIs block content classified at the highest severity level for harmful categories. This safeguard prevents the model from returning output that could pose safety risks to users. Developers can still lower the filter settings for less sensitive scenarios, but the system ensures high-risk content is not returned even when filters are adjusted. This helps maintain a baseline safety standard for any application using Phi Silica.

**Security and secure execution environment**

Phi Silica operates through the Windows AI APIs within the Windows App SDK, which benefit from Windows' built-in security model. This architecture ensures the model runs inside the trusted environment of the operating system rather than being exposed over external endpoints. Because the model runs entirely on-device, user inputs and outputs remain local, reducing exposure to network-based security risks.

## Best practices for deploying and adopting Phi Silica

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

**Deployers and end-users should:**

**Exercise caution and monitor outcomes when using Phi Silica for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

**Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

**End-users should:**

**Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Phi Silica and verify that they match their expectations and requirements.

**Be aware of the risk of overreliance**: Overreliance on AI happens when users accept incorrect or incomplete AI outputs, mainly because mistakes in AI outputs may be hard to detect. For the end-user, overreliance could result in decreased productivity, loss of trust, application abandonment, financial loss, psychological harm, physical harm, among others (e.g., a doctor accepts an incorrect AI output).

**Exercise caution when designing agentic AI in sensitive domains**: Users should exercise caution when designing and/or deploying agentic AI applications in sensitive domains where agent actions are irreversible or highly consequential. Additional precautions should also be taken when creating autonomous agentic AI as described further in either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals).

**Deployers should:**

**Ensure the deployment environment meets hardware and OS requirements**: Deployers should verify that Phi Silica is used on supported Windows devices – particularly Copilot+ PCs with Neural Processing Units (NPUs), where the model is optimized to run efficiently. Using the model on unsupported devices may lead to API errors and feature failures. Before rolling out to users, confirm that the operating system, device hardware, and Windows AI APIs are all up to date.

**Configure content-safety settings appropriately for the scenario**: Phi Silica uses the Windows AI APIs' built-in content-moderation system, which lets deployers adjust severity levels for categories such as hate, violence, sexual content, and self-harm. These settings determine how strictly input and output text is filtered. Deployers should test different severity levels to strike the right balance between safety and usability, ensuring that content moderation behaves predictably without over-blocking legitimate content.

**Guide users on how to get consistent results**: Deployers should provide clear instructions for how to use the model effectively, such as offering examples of well-structured prompts or recommending certain Text Intelligence Skills (summarize, rewrite, text-to-table) based on the intended use. This helps end-users understand how to phrase inputs and when to expect structured responses. Deployers may also want to set sensible default settings for things like rewrite tone or summarization length, depending on the application.

**Perform scenario-specific testing before rollout**: Because performance and output quality can vary with input length, format, and device conditions, deployers should evaluate the model with the same types of prompts and workloads their users will rely on. For example, if the application expects users to summarize long text, test Phi Silica using representative samples. If the application depends on rewriting content with specific tones, verify that the model consistently generates the expected style. Early testing allows deployers to identify predictable limitations – such as context-window constraints or formatting inconsistencies – and address them before deployment.

**Provide clear support paths for predictable or known failures**: Deployers should help users understand how to recover from common issues such as long input text exceeding the model's context window, unexpected moderation blocks, or inconsistent formatting. This may involve providing troubleshooting guidance (e.g., shorten inputs, adjust moderation thresholds, or reformat the prompt). Offering simple recovery steps reduces friction and increases user confidence in the system.

**Monitor usage and watch for performance drift**: After deployment, deployers should establish ways to monitor how Phi Silica behaves over time. This can include tracking response latency, error rates, completeness of generated outputs, or changes in content-moderation triggers. Monitoring helps detect regressions that may occur due to application changes, OS updates, or shifts in user behavior. When drift is detected, deployers can adjust configuration, retrain or update LoRA adapters (if used), or provide updated guidance to end-users.

## Learn more about Phi Silica

For additional guidance on the responsible use of Phi Silica, we recommend reviewing the additional documentation below.

- [Get started with Phi Silica in the Windows App SDK | Microsoft Learn](/windows/apps/windows-ai/phi-silica)
- [Content safety moderation with the Windows AI APIs | Microsoft Learn](/windows/ai/content-safety)
- [Responsible Generative AI Development on Windows | Microsoft Learn](/windows/ai/responsible-ai)

**Learn more about responsible AI**

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/azure/machine-learning/concept-responsible-ai)
