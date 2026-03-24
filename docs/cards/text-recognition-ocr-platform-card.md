---
title: Platform card - Text Recognition (OCR)
description: Learn about Text Recognition (OCR) features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 03/24/2026
ms.topic: concept-article
ms.service: windows
---

# Platform card: Microsoft Foundry on Windows – Text Recognition (OCR)

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how enterprise customers and individuals can engage with AI responsibly.

## Overview

Text Recognition (OCR) in Microsoft Foundry on Windows enables applications to extract machine-readable text from images and documents directly on a Windows device. This capability allows apps to recognize printed or handwritten text from photos, scanned documents, screenshots, or camera frames without sending data to the cloud. OCR is exposed through the Windows AI APIs in the Windows App SDK. Developers can submit image inputs and receive structured text results that include recognized text content and its spatial layout. Because OCR runs locally on the device, it provides low-latency performance, works offline, and keeps user data on the device, which can be important for privacy-sensitive or enterprise scenarios. The Text Recognition APIs are optimized for Windows devices and designed to integrate seamlessly into Windows applications. They are intended to support common text-extraction scenarios rather than serve as a general-purpose document understanding system.

## Key terms

The following list provides a glossary of key terms related to Text Recognition (OCR) APIs:

| Term | Definition |
|------|------------|
| **Bounding Box** | A rectangular region in an image that indicates where recognized text appears. |
| **Image Input** | A bitmap or image frame provided to the OCR API for text recognition. |
| **Inference** | The process of running the OCR model locally to detect and recognize text in an image. |
| **Local Model** | An AI model that runs directly on the user's device rather than on the cloud. Text Recognition (OCR) model in this document operates entirely on-device for speed, privacy, and reliability. |
| **NPU (Neural Processing Unit)** | A specialized chip designed to speed up AI workloads while using less power than a CPU or GPU. Phi Silica is optimized to run efficiently on Windows devices with NPUs. |
| **Optical Character Recognition (OCR)** | A computer vision technique that detects and converts text within images into machine-readable text. |
| **Recognized Text** | The textual output produced by the OCR system, typically grouped into lines or words. |
| **Text Line** | A sequence of recognized characters grouped together based on layout and proximity. |
| **Windows AI APIs** | A set of Windows developer APIs that provide access to on-device AI capabilities such as OCR. |
| **Windows Copilot+ PCs** | A class of Windows devices equipped with next-generation NPUs and system optimizations that enable high-performance local AI workloads. Text Recognition API is tuned specifically for these PCs. |
| **Windows App SDK** | A development framework that provides modern Windows APIs, including Windows AI APIs, for building Windows applications. |

## Key features or capabilities

The following capabilities describe what Text Recognition (OCR) is designed to do.

**On-device text recognition**

The OCR capability extracts text from images locally on the Windows device. No network connection is required, and image data does not leave the device during processing.

**Printed and handwritten text recognition**

The API supports recognizing both printed text and common forms of handwritten text, depending on image quality and language support.

**Structured text output**

OCR returns recognized text in a structured format, typically organized into lines and regions, along with bounding boxes that describe where text appears in the image.

**Language awareness**

The OCR system can automatically detect and recognize text in supported languages, enabling applications to process multilingual content without explicit language selection in many scenarios.

**Windows AI API integration**

All OCR functionality is accessed through the Windows AI APIs in the Windows App SDK, providing a consistent developer experience and integration model across Windows applications.

## Intended uses

Text Recognition (OCR) is designed for a broad range of productivity, accessibility, and enterprise scenarios, including:

- **Extracting text from images and screenshots**: Applications can allow users to select an image or screenshot and extract readable text for copying, searching, or further processing.
- **Digitizing scanned documents**: OCR can convert scanned paper documents into searchable and selectable text, enabling downstream workflows such as indexing or archiving.
- **Accessibility support**: OCR can help surface text from images for screen readers or assistive technologies, improving accessibility for users with visual impairments.
- **In-app text understanding**: Applications can use OCR to recognize text within app-specific images, such as receipts, forms, or instructional materials, to support user workflows.
- **Offline and privacy-sensitive scenarios**: Because OCR runs locally, it is suitable for scenarios where network connectivity is unavailable or where users prefer not to send image data to cloud services.

## Models and training data

Text Recognition (OCR) in Windows is powered by Microsoft-developed machine learning models trained to recognize text in images. These models are trained using a combination of licensed data, data created by human trainers, and publicly available text-image data, in accordance with Microsoft's Responsible AI standards. For additional information on Microsoft's approach to responsible AI development, please refer to this link: [Responsible Generative AI Development on Windows](/windows/ai/responsible-ai).

## Performance

OCR performance depends on factors such as image resolution, lighting conditions, text orientation, font style, and language. The system performs best when text is clearly visible, well-lit, and reasonably aligned. Because processing occurs locally, latency is predictable and not affected by network conditions. Performance may vary across devices depending on hardware capabilities and current system load.

OCR results are typically returned quickly for standard image sizes. Very large images or images containing dense text may require additional processing time.

## Limitations

Understanding Text Recognition (OCR)'s limitations is crucial to determine if it is used within safe and effective boundaries. While we encourage customers to leverage Text Recognition (OCR) in their innovative solutions or applications, it's important to note that Text Recognition (OCR) was not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals) as well as the following considerations when choosing a use case:

- **Image quality sensitivity**: Low-resolution images, motion blur, glare, or extreme lighting conditions can reduce recognition accuracy.
- **Complex layouts**: Highly complex document layouts, overlapping text, or decorative fonts may lead to partial or incorrect recognition.
- **Handwriting variability**: Handwritten text recognition accuracy varies significantly based on writing style, legibility, and language.
- **Non-textual content**: OCR is designed to recognize text only. It does not interpret images, diagrams, or semantic meaning beyond extracted text.
- **Not a decision-making system**: OCR outputs raw recognized text and layout information. It does not validate correctness, interpret meaning, or make decisions based on the extracted content.

## Evaluations

Performance and safety evaluations assess whether AI applications are operating reliably and securely by examining factors like groundedness, relevance, and coherence while identifying the risks of generating harmful content. The following evaluations were conducted with safety components already in place, which are also described in [Safety components & mitigations](#safety-components--mitigations).

### Performance & quality evaluations

Evaluations focus on text detection accuracy, character recognition quality, and layout consistency across a variety of image types, languages, and device configurations.

### Risk & safety evaluations

Because OCR is a non-generative, perception-based capability, safety evaluations focus on reliability, robustness, and predictable behavior rather than content generation risks.

## Safety components & mitigations

**Local execution and data privacy**

OCR runs entirely on-device, which reduces privacy risks associated with transmitting images or extracted text over the network.

**Predictable, non-generative behavior**

OCR only extracts text that appears in the input image. It does not generate new content, infer intent, or provide interpretations beyond recognized characters.

**Transparency of outputs**

The API returns explicit recognition results and bounding boxes, allowing developers to inspect, validate, or discard outputs as appropriate for their application.

## Best practices for deploying and adopting Text Recognition (OCR)

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

**Deployers and end-users should:**

**Exercise caution and evaluate outcomes when using OCR for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

**Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

**End-users should:**

**Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Text Recognition (OCR) and verify that they match their expectations and requirements.

**Be aware of the risk of overreliance**: Overreliance on AI happens when users accept incorrect or incomplete AI outputs, mainly because mistakes in AI outputs may be hard to detect. For the end-user, overreliance could result in decreased productivity, loss of trust, application abandonment, financial loss, psychological harm, physical harm, among others (e.g., a doctor accepts an incorrect AI output).

**Exercise caution when designing agentic AI in sensitive domains**: Users should exercise caution when designing and/or deploying agentic AI applications in sensitive domains where agent actions are irreversible or highly consequential. Additional precautions should also be taken when creating autonomous agentic AI as described further in either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals).

**End-users should also:**

- **Human-in-the-Loop Review**: Apply human review when OCR results are used for important decisions or records.
- **Validated Output Use**: Avoid overreliance on OCR outputs without validation.

**Deployers should:**

- **Representative Image Testing**: Test OCR using representative images from real-world usage.
- **Image Quality Best Practices**: Provide guidance to users on image quality for best results.
- **Failure-Resilient Experience**: Handle recognition failures gracefully by allowing retries or manual correction.
- **Decision-Support Safeguards**: Avoid using OCR as the sole source of truth in consequential decision-making systems.

## Learn more about Text Recognition (OCR)

For additional guidance, see the following resources:

- [Text Recognition (OCR) API documentation | Microsoft Learn](/windows/ai/ocr)
- [Text Recognition tutorial | Microsoft Learn](/windows/ai/ocr-tutorial)
- [Responsible Generative AI Development on Windows | Microsoft Learn](/windows/ai/responsible-ai)

**Learn more about responsible AI**

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](https://learn.microsoft.com/azure/machine-learning/concept-responsible-ai)
