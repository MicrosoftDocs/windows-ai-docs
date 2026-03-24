---
title: Application card - Microsoft Paint
description: Learn about Microsoft Paint's AI features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 02/19/2026
ms.topic: concept-article
ms.service: windows
---

# Application card: Microsoft Paint

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and code of conduct, which outline how [enterprise customers] (https://review.learn.microsoft.com/en-us/legal/ai-code-of-conduct?branch=main) and [individuals] (https://www.microsoft.com/en-us/servicesagreement#3_codeOfConduct) can engage with AI responsibly.

## Overview

Microsoft Paint is a built-in Windows application that enables users to create, edit, and modify digital images. Recent updates introduce AI assisted features that support image generation and editing through natural inputs such as text descriptions and sketches. Depending on the feature and device, Paint uses a combination of on device AI models and cloud based AI services to assist with tasks like generating visual concepts, transforming sketches into images, or making targeted edits. These capabilities are designed to augment traditional editing workflows, allowing users to review, accept, modify, or discard AI generated output as part of the creative process.

Paint's AI features aim to lower the barrier to visual creation and image editing by providing lightweight, integrated tools within a familiar application. For example, features such as Cocreator on Copilot+ PCs run locally on supported devices, while other features, such as Image Creator, rely on cloud-based AI services and require a Microsoft account. This approach allows Microsoft to balance performance, device capabilities, and responsible use considerations across different user environments. Paint's AI features are intended for consumers, students, hobbyists, and knowledge workers who need simple image creation or editing tools without requiring professional design software.

Microsoft provides documentation, demonstrations, and usage guidance for Paint's AI features through Microsoft Support and Windows documentation, including information about system requirements and feature availability. These resources help users and enterprise decision makers understand how AI is used in the application, what capabilities are supported, and how those capabilities align with Microsoft's broader commitment to responsible AI. Here is the link to support article describing generative AI features on Copilot+PCs - [Use Copilot+ PC features in Paint](https://support.microsoft.com/windows/use-copilot-pc-features-in-paint-53857513-e36c-472d-8d4a-adbcd14b2e54), here is the link to support article for generative AI features that are more broadly available - [Use Image Creator in Paint to generate AI art](https://support.microsoft.com/windows/use-image-creator-in-paint-to-generate-ai-art-107a2b3a-62ea-41f5-a638-7bc6e6ea718f).

## Key terms

The following list provides a glossary of key terms related to Microsoft Paint:

**Copilot+ PC**: A Windows device designed to support advanced AI workloads locally on the device using specialized hardware.

**Generative AI**: A type of AI that creates new content, such as images, based on user inputs instead of retrieving existing content.

**Prompt**: Text provided by a user to guide the AI in generating or editing an image.

## Key features or capabilities

The Microsoft Paint application includes AI powered features designed to help users create, modify, and enhance images using natural inputs such as text descriptions and sketches. These features are integrated into the Paint editing experience and are intended to support creative exploration while keeping users in control of final outcomes.

- **Cocreator (text and sketch based image creation)**: Cocreator allows users to generate artwork by combining a text description with freehand drawing on the Paint canvas. As the user draws, the system generates image variations based on both the text prompt and the sketch, allowing users to guide the output visually and adjust the level of AI creativity. The generated result can be applied to the canvas and edited further using standard Paint tools.

- **Image Creator (text to image generation)**: Image Creator enables users to generate images directly from text descriptions without requiring an initial sketch. Users enter a prompt describing the image they want, and Paint produces multiple image options that can be selected, refined, and reused. This capability is designed to help users quickly create visual concepts or starting points for creative projects.

- **Generative fill (AI assisted image editing)**: Generative fill allows users to add new elements to an existing image by selecting an area and describing what they want to insert. The feature generates multiple options that blend into the selected region, giving users the ability to choose, retry, or refine results before applying changes to the image. This supports iterative editing without requiring advanced manual techniques.

- **Sticker generator (custom visual elements)**: The sticker generator creates small, reusable graphics from short text prompts. Users can browse generated sticker options and add them to the canvas, copy them, or save them for later use. This feature is intended to help users quickly add personalized visual elements to images, documents, or messages.

- **Object select (AI assisted selection)**: Object Select uses AI to automatically detect and isolate objects within an image. Instead of manually tracing shapes, users can select an object with a click and then move, edit, erase, or apply other Paint actions to it. This capability is designed to make precise edits faster and more accessible for nonexpert users.

- **Hybrid on device and cloud supported operation**: Many Paint AI features run locally on supported devices, using specialized hardware such as a neural processing unit (NPU) when available. Cloud services are used to provide safety systems, including content filtering, to support responsible use of AI features while image generation itself occurs on the user's device.

- **Content credentials and transparency signals**: Images generated using generative AI include content credentials based on the C2PA standard. These credentials help identify images that were created using AI, supporting transparency for users and downstream viewers of generated content.

> [!NOTE]
> Microsoft Paint is not designed or marketed as an autonomous or agentic AI system. The AI features do not independently plan tasks or act without user input. Users initiate all actions, guide generation through prompts or sketches, and decide whether to keep, modify, or discard generated results.

## Intended uses

Microsoft Paint can be used in multiple scenarios across a variety of industries. The following examples illustrate how end users and organizations may use Paint's AI features, and the benefits they may experience.

- **Creative content creation for media and consumer uses**: Individuals and small media teams can use Image Creator in Paint to generate visual concepts from text descriptions, such as illustrations for presentations, blog posts, or social media. This helps users quickly explore ideas and create visual starting points without professional design tools. Users remain in control by choosing which generated images to keep, edit, or discard.

- **Education and learning materials**: Teachers and students can use Paint to create simple visuals, diagrams, or illustrations to support lessons and assignments. AI assisted image generation and editing can reduce the time required to produce visual aids and make creative tools more accessible to users with varying technical skills. This is a lowstakes use where AI output supports learning rather than critical decisionmaking.

- **Product ideation and early-stage design**: Teams in industries such as consumer goods or software development can use Paint to explore early visual ideas, such as packaging concepts or interface illustrations. AIgenerated images serve as rough concepts rather than final designs, enabling faster brainstorming while preserving clear boundaries for approval and final production.

Microsoft Paint and its AI features are not an autonomous or agentic AI system. The application does not act independently, plan actions, or make decisions on behalf of users. All AI capabilities are userinitiated, operate within defined feature boundaries, and require explicit user input and approval before changes are applied to images.

## Models and training data

Microsoft Paint leverages a variety of AI models to power the experience that users see. These models support image generation, image editing, and AI assisted content creation features available in the application.

Examples of models and AI services used include:

- **Microsoft image generation and image understanding models**: Microsoft Paint includes generative AI features powered by AI models that run directly on supported devices. These models are optimized and finetuned to support core image understanding and image editing experiences in the Paint app.

- **Azure OpenAI Service models**: Some Paint AI features rely on models provided through Azure OpenAI Service to enable text to image generation capabilities. Details about these models, including training data considerations and responsible use practices, are described in the [Azure OpenAI Service transparency note](/azure/ai-foundry/responsible-ai/openai/transparency-note).

To learn more about the data used to train the foundation models behind Microsoft Paint, refer to the linked model cards and transparency notes for the relevant models and services. These resources provide additional information about model purpose, training data sources, limitations, and responsible AI considerations.

## Performance

Microsoft Paint's AI features are designed for everyday personal, educational, and workplace environments using Windows 11 and supported hardware for NPU based features. Reliability depends on factors such as device capabilities, network availability where cloud connection and sign-in is required, and adherence to the intended workflow where users review and apply AI generated outputs. When used as designed, Paint's AI features can be used repeatedly to generate, edit, and refine images.

Paint supports both on device and cloud based AI processing. On supported Copilot+ PCs, certain features run locally and are optimized for devices that meet Microsoft's hardware and software requirements. Other features rely on Microsoft cloud services for running AI models. In both cases, user is required to sign-in with a Microsoft account to access the features.

The application supports multiple input and output modalities. Intended inputs include text prompts entered by the user, freehand sketches drawn directly on the canvas, and existing image files that users choose to edit. Expected outputs include generated images, edited images, image regions filled or modified using AI assistance, and reusable visual elements such as stickers. All outputs are presented to the user for review before being applied to the canvas.

Paint's AI features are designed to work with the same languages that Windows and Microsoft consumer services commonly support. Though all these languages are supported, some features like Cocreator are optimized for prompts in English and other languages may experience degraded performance.

## Limitations

Understanding Microsoft Paint's limitations is crucial to help ensure it is used within safe and effective boundaries. While we encourage customers to leverage Microsoft Paint in their innovative solutions or applications, it's important to note that Microsoft Paint was not designed for every possible scenario. We encourage users to refer to either the Microsoft [Enterprise AI Services Code of Conduct (for organizations)] (https://review.learn.microsoft.com/en-us/legal/ai-code-of-conduct?branch=main) or the [Code Conduct section in the Microsoft Services Agreement (for individuals)](https://www.microsoft.com/en-us/servicesagreement#3_codeOfConduct).

- **Dependence on device capabilities**: Microsoft Paint's AI features may perform differently depending on the device hardware, especially for features designed to run locally on supported devices. Performance may degrade or features may become unavailable if required system resources are limited.

- **Language coverage and localization limitations**: Microsoft Paint's AI features were developed and tested primarily using English language inputs. While the user interface supports the languages available in Windows, AI generated outputs may be less accurate or less reliable when prompts are provided in unsupported or less represented languages. Users should exercise caution when using the application outside the intended language scope, especially for scenarios where accuracy or clarity is critical.

- **Variability in AI generated outputs**: AI generated images and edits in Microsoft Paint can vary based on the content and clarity of user inputs. The application is designed to provide creative assistance rather than deterministic or guaranteed results. Users should carefully review all AI generated content before using it in downstream scenarios, particularly when accuracy, consistency, or compliance requirements apply.

- **Representativeness of training data**: As with many AI systems, the models supporting Microsoft Paint may reflect limitations in the data used to train them. Outputs related to underrepresented populations, styles, or cultural contexts may be less accurate or less reflective of real world expectations. This limitation is important for users to consider when choosing use cases that involve sensitive, highimpact, or representational content.

## Evaluations

Performance and safety evaluations assess whether AI applications are operating reliably and securely by examining factors like groundedness, relevance, and coherence while identifying the risks of generating harmful content. The following evaluations were conducted with safety components already in place, which are also described in the Safety Components and Mitigations section.

### Risk and safety evaluations

Evaluating potential risks associated with AI-generated content is essential for safeguarding against content risks with varying degrees of severity. This includes evaluating an AI application's predisposition towards generating harmful content or testing for vulnerabilities to jailbreak attacks. For Microsoft Paint, we conducted risk and safety evaluations for the following metrics available through Microsoft Foundry:

- Hate and unfairness
- Sexual
- Violence
- Self-harm

#### Evaluation data for quality and safety

Our evaluation data is custom-built to assess AI application performance across key areas of safety and quality, simulating real-world scenarios and risks. We begin by identifying relevant evaluation aspects of concern based on multi-disciplinary research and expert input. These concerns are translated into targeted evaluation objectives and guide formulation of evaluation metrics. For safety, we create adversarial prompts to elicit undesirable or edge-case responses, which are then scored using AI-assisted annotators trained to assess alignment with Microsoft's safety standards. For quality, we craft rubric-based prompts relevant to scenarios including evaluating retrieval-augmented generation (RAG) applications and agents. Datasets are curated from diverse sources including synthetic and public datasets to simulate real-world user scenarios. Using the curated datasets, both evaluations undergo iterative refinement and human alignment to improve metric efficacy and reliability. This methodology forms the foundation of repeatable, rigorous assessments that reflect how customers use evaluations to build better and safer AI.

## Safety components and mitigations

**Group policy to control feature enablement**: For organizations which do not want to allow generative AI or any specific feature for employees, they can use group policy to manage which features are allowed vs. not. All the disabled features are hidden from the users in the UI.

## Best practices for deploying and adopting Microsoft Paint

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

### Deployers and end-users should

- **Exercise caution and monitor outcomes when using Microsoft Paint for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

- **Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

### End-users should

- **Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Microsoft Paint and verify that they match their expectations and requirements.

- **Avoid prohibited uses**: Microsoft Paint must not be used to create content that violates applicable laws, Microsoft policies, or content safety requirements, including harmful, deceptive, or misleading content. Users are prohibited from using the application to generate content intended to deceive, manipulate, or harm individuals or groups. All use of Microsoft Paint is subject to Microsoft's terms of service and responsible AI guidelines.

- **Submit feedback or report issues as they arise**: End-users can submit feedback or report issues directly through the in-product feedback button in the UI. For urgent security or privacy concerns, use the dedicated security reporting channel provided by Microsoft.

### Deployers should

- **Use group policy to control access**: For organizations which do not want to allow generative AI or any specific feature for employees, they can use group policy to manage which features are allowed vs. not. All the disabled features are hidden from the users in the UI.

## Learn more about Microsoft Paint

For additional guidance on the responsible use of Microsoft Paint, we recommend reviewing the additional documentation the following.

- [Use Copilot+ PC features in Paint](https://support.microsoft.com/windows/use-copilot-pc-features-in-paint-53857513-e36c-472d-8d4a-adbcd14b2e54)
- [Use Image Creator in Paint to generate AI art](https://support.microsoft.com/windows/use-image-creator-in-paint-to-generate-ai-art-107a2b3a-62ea-41f5-a638-7bc6e6ea718f)

### Learn more about responsible AI

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/training/paths/responsible-ai-business-principles/)
