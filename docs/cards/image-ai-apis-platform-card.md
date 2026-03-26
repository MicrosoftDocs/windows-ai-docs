---
title: Platform card - Image AI APIs
description: Learn about Image AI APIs' features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 03/24/2026
ms.topic: concept-article
ms.service: windows
---

# Platform card: Microsoft Foundry on Windows – Image AI APIs

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how enterprise customers and individuals can engage with AI responsibly.

## Overview

Microsoft Foundry on Windows offers a set of on-device Image AI APIs and capabilities that enable developers to analyze, modify, and enhance images. These APIs include:

- **Foreground extraction** – isolate the main subject from a background.
- **Object erase** – remove specified objects from an image.
- **Object extraction** – detect and extract objects or regions of interest.
- **Super-resolution** – enhance image detail and resolution.
- **Image description** – generate textual descriptions summarizing image content.

All Image AI APIs are available through the Windows App SDK and run locally on the device, offering low latency, predictable performance, and the ability to operate without cloud connectivity. Because processing happens on the device, user image data does not need to be transmitted over the network, supporting privacy-sensitive scenarios. These APIs are designed as building blocks for imaging applications, providing capability primitives that developers can integrate into photo editing, accessibility, productivity, or creative workflows.

## Key terms

The following list provides a glossary of key terms related to Image AI APIs:

| Term | Definition |
|------|------------|
| **Bounding Box** | A rectangular region that defines the position of a detected object within an image. |
| **Foreground** | The part of an image that represents the primary subject, separated from the background. |
| **Image Input** | A bitmap or image frame supplied to an image API for processing. |
| **Inference** | The execution of an AI model on image data to generate outputs such as labels, masks, descriptions, or enhanced imagery. |
| **Local Model** | An AI model that runs directly on the user's device rather than in the cloud. Phi Silica operates entirely on-device for speed, privacy, and reliability. |
| **Mask** | A binary or multi-class overlay that indicates pixels belonging to a specific region (e.g., foreground or erased area). |
| **Object Region** | A detected section of an image containing a recognizable object of interest. |
| **Output Image** | The resulting image after applying an image transformation or enhancement. |
| **Super-Resolution** | A processing task that increases the apparent resolution and detail of an image using AI models. |
| **Text Description** | A natural language summary describing the contents or context of an image. |
| **Windows AI APIs** | A set of Windows developer interfaces that provide access to on-device AI models like Phi Silica for text generation, image scaling, and other tasks. |
| **Windows Copilot+ PCs** | A class of Windows devices equipped with next-generation NPUs and system optimizations that enable high-performance local AI workloads. Phi Silica is tuned specifically for these PCs. |
| **Windows App SDK** | A development framework that provides modern Windows APIs - including Windows AI APIs - for building Windows applications. It is the primary integration path for Phi Silica. |

## Key features or capabilities

The key features and capabilities outlined here describe what each Image AI API provides and how it performs across supported tasks.

**Image Foreground Extraction**

This capability analyzes an input image to separate the foreground subject from the background. It returns a mask or transparent cutout that can be used to compose new background contexts or apply effects.

**Object Erase**

This API accepts an image and regions to remove (defined by masks or bounding boxes) and produces a new image with those objects removed and plausible background filled in. Erase operations are designed to preserve surrounding visual consistency.

**Object Extraction**

Object extraction identifies and localizes objects or regions of interest in an image. Results include object boundaries, labels (if available), and associated geometry for use in downstream workflows.

**Super-Resolution**

Super-resolution enhances the spatial detail of an image, allowing developers to upscale low-resolution content with improved sharpness and visual fidelity compared to standard interpolation.

**Image Description**

Image description generates a textual summary that highlights salient content within an image. Descriptions are intended to provide a human-readable overview of what an image contains, supporting scenarios such as accessibility and search.

## Intended uses

Image AI APIs are designed for a range of use cases across accessibility, creative tools, productivity apps, and automated image workflows:

- **Photo editing and creation**: Developers can build editing features such as background removal, object cleanup, and resolution enhancement directly into applications.
- **Content accessibility**: Image description can provide alt-text or narrative summaries for users with visual impairments or assistive technologies.
- **Media enhancement**: Super-resolution can improve the quality of images from low-resolution sources, such as zoomed-in photos or historical media.
- **Automated processing pipelines**: Object extraction and foreground segmentation can power indexing, tagging, or routing in workflows that handle large volumes of images.
- **Presentation and content reuse**: Foreground extraction and object erase support repurposing visual assets for new contexts, such as compositing in slides, documents, or web content.

## Models and training data

The Image AI APIs in Windows leverage machine learning models trained to perform perception tasks such as segmentation, object detection, image enhancement, and captioning. These proprietary models are developed and trained by Microsoft using a combination of licensed data, publicly available data, and human-annotated images, in accordance with Microsoft's Responsible AI standards. For more information on Microsoft's approach to Responsible AI development, please refer to this link: [Responsible Generative AI Development on Windows](/windows/ai/responsible-ai).

## Performance

Performance of the Image AI APIs varies according to image complexity, resolution, and device hardware. Because all processing occurs locally on the device, latency is predictable and not dependent on network connectivity. However, the execution time and resource usage can vary based on image size and available compute resources. Typically:

- Foreground extraction and object detection perform quickly on standard-resolution images.
- Super-resolution incurs additional computation for high-fidelity enhancement.
- Image description performance reflects the complexity of running an on-device small language model to summarize the content.

Developers should test performance with representative workloads to validate responsiveness for their specific scenarios.

## Limitations

Understanding Image AI APIs' limitations is crucial to determine if they are used within safe and effective boundaries. While we encourage customers to leverage Image AI APIs in their innovative solutions or applications, it's important to note that Image AI APIs were not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals) as well as the following considerations when choosing a use case:

- **Low image quality**: Blur, extreme lighting, occlusions, or noise can reduce accuracy in object segmentation, erase quality, or recognition.
- **Semantic inference boundaries**: Image description provides a high-level summary but does not guarantee complete or exhaustive interpretation of every scene element. It is not intended to replace human judgment or detailed semantic analysis.
- **Non-guaranteed correctness**: Object extraction and labeling do not ensure category accuracy in all cases; recognition failures and false positives are possible, especially for unusual or ambiguous objects.
- **Not an autonomous decision system**: These capabilities extract or summarize visual content; they do not make decisions or assert facts about real-world entities beyond the image evidence.
- **Device variability**: Different devices deliver different performance and resource availability. High-resolution processing may be slower on less powerful hardware.

## Evaluations

### Performance & quality evaluations

Image AI APIs are evaluated to ensure reliable outputs across supported tasks. Evaluations include recognition accuracy for segmentation and object extraction, visual fidelity for super-resolution, and descriptiveness for image captioning.

### Risk & safety evaluations

Safety evaluations focus on robustness, predictable behavior, and mitigation of failure modes that could lead to misinterpretation. For image description, extra care is taken to avoid harmful or biased language, aligning with Responsible AI principles. Outputs are reviewed for consistency and appropriateness across diverse content.

## Safety components & mitigations

**Local execution and data privacy**

All Image AI APIs in Windows run on the device, keeping image data local and reducing privacy risks associated with transmitting sensitive images over networks.

**Predictable, non-generative behavior**

Except for image description—which produces natural language summaries—these APIs do not generate new semantic content. Object extraction, foreground masks, erase results, and super-resolution outputs are deterministic transformations of input images.

**Content safety awareness**

For image description, safeguards are applied to reduce the risk of harmful or biased text. Outputs should still be validated in high-sensitivity contexts. Developers may implement filters or post-processing to enforce additional safety policies.

**Transparency of outputs**

The APIs provide explicit structured outputs (e.g., masks, bounding boxes, enhanced pixels, text summaries) that developers can validate, inspect, or filter before use in downstream logic or UI.

## Best practices for deploying and adopting Image AI APIs

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

**Deployers and end-users should:**

**Exercise caution and evaluate outcomes when using Image APIs for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

**Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

Be aware that image understanding tasks may be imperfect, and outputs should be reviewed when used in consequential workflows. Avoid overreliance on automated image summaries or segmentation without human verification in legal, medical, or other sensitive domains.

**End-users should:**

**Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Image APIs and verify that they match their expectations and requirements.

**Be aware of the risk of overreliance**: Overreliance on AI happens when users accept incorrect or incomplete AI outputs, mainly because mistakes in AI outputs may be hard to detect. For the end-user, overreliance could result in decreased productivity, loss of trust, application abandonment, financial loss, psychological harm, physical harm, among others (e.g., a doctor accepts an incorrect AI output).

**Exercise caution when designing agentic AI in sensitive domains**: Users should exercise caution when designing and/or deploying agentic AI applications in sensitive domains where agent actions are irreversible or highly consequential. Additional precautions should also be taken when creating autonomous agentic AI as described further in either the [Microsoft Enterprise AI Services Code of Conduct](https://www.microsoft.com/ai/responsible-ai) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals).

Apply human oversight in final content use, especially when image descriptions are used to drive accessibility tools or decisions.

**Deployers should:**

- **Diverse Real-World Testing**: Test with realistic images that reflect the diversity of user content.
- **Optimal Input Guidance**: Provide guidance to users on recommended image sizes and quality for best results.
- **Confidence-Aware Output Handling**: Gracefully handle recognition uncertainty, such as low confidence or ambiguous extractions.
- **User-Verified Edits**: Offer user controls to confirm edits made via object erase or foreground extraction.
- **Latency-Optimized Performance**: Validate performance on target hardware and adjust UI flows to minimize latency impact.

## Learn more about Image AI APIs

For additional guidance on the responsible use of Image AI APIs, we recommend reviewing the following documentation.

- [Image Foreground Extractor API | Microsoft Learn](/windows/ai/image-foreground-extractor)
- [Image Object Erase API | Microsoft Learn](/windows/ai/image-object-erase)
- [Image Object Extractor API | Microsoft Learn](/windows/ai/image-object-extractor)
- [Image Super-Resolution API | Microsoft Learn](/windows/ai/image-super-resolution)
- [Image Description API | Microsoft Learn](/windows/ai/image-description)
- [Responsible Generative AI Development on Windows | Microsoft Learn](/windows/ai/responsible-ai)

**Learn more about responsible AI**

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/azure/machine-learning/concept-responsible-ai)
