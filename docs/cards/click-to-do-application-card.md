---
title: Application card - Click to Do
description: Learn about Click to Do's AI features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 02/19/2026
ms.topic: concept-article
ms.service: windows
---

# Application card: Click to Do

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how [enterprise customers](/legal/ai-code-of-conduct) and [individuals](https://www.microsoft.com/servicesagreement) can engage with AI responsibly.

## Overview

Click to Do is a Windows feature that provides intelligent shortcuts to consumers so they can quickly complete a task based on what they are viewing. Instead of switching apps or copying information, the user can perform tasks right from their screen. For example, if they see an email, they can send a message or schedule a meeting with Teams; if there's a phrase in another language, the user can translate it instantly. The goal is to save time and keep users focused by reducing unnecessary steps.

Click to Do identifies text and images currently on the screen to suggest helpful actions, like editing images or searching the web for related content. The user stays in control—Click to Do processes data locally and allows users to choose if they want more online content.

[Click to Do: do more with what's on your screen](https://support.microsoft.com/en-us/windows/click-to-do-do-more-with-what-s-on-your-screen-6848b7d5-7fb0-4c43-b08a-443d6d3f5955)

## Key terms

The following list provides a glossary of key terms related to Click to Do:

| Term | Definition |
|------|------------|
| **AI system component** | Any part of a system that includes AI. |
| **Customers** | Any parties who have entered contracts with Microsoft to license, subscribe and/or use Microsoft products or services. |
| **Entity** | Onscreen selectable object (text or image) recognized by Click to Do. |
| **Handoff actions** | Actions that open a full app to complete the task (e.g., Word, Photos). |
| **Human oversight** | Supervision of system behavior or outcomes by people. |
| **Large Language Models (LLMs)** | AI models that are trained on large amounts of text data to predict words in sequences. This enables them to perform a variety of language tasks, such as text generation, summarization, translation, classification, and more. |
| **Metaprompt** | Instructions prepended or appended to user prompts that guide the system's behavior. |
| **Mitigation** | A method or combination of methods designed to reduce potential harms that may result from using AI-driven features. |
| **Prompt or query** | The text a user submits as an input to the product. |
| **Response** | The text output in response to a prompt or query. Synonyms for "response" include "completion," "generation," and "answer." |
| **System actions / Inbox actions** | Windows-provided actions. |

## Key features or capabilities

The key features and capabilities outlined here describe what Click to Do is designed to do and how it performs across supported tasks.

- **Text Actions**: Click to Do lets users select any visible text on their screen and instantly perform actions like copying, opening in a text editor, or searching the web. For longer selections (10+ words), advanced AI-powered options become available, including summarizing, creating bulleted lists, and rewriting text in casual, formal, or refined tones—all processed locally using the Phi Silica model.

- **Image Actions**: When users select an image or object on screen, Click to Do offers quick options like copying, saving, sharing, and opening with apps like Photos or Paint. It also enables visual search with Bing, background blurring, object erasing, and background removal—streamlining basic image editing without launching separate tools.

- **Communication Shortcuts**: If a selected text includes an email address, Click to Do provides direct options to send an email, start a Teams message, or schedule a Teams meeting. These actions integrate with the user's default communication apps, making it easy to connect with contacts directly from any screen.

- **Copilot Integration**: For any selected content—text or images—Click to Do offers an "Ask Copilot" option. This opens Microsoft Copilot with the selected content preloaded, allowing users to ask questions, get descriptions, translate text, or perform deeper AI-driven tasks. If the user has a Pro, Enterprise, or Education version of Windows, Microsoft 365 Copilot is used instead.

- **Unit Conversion**: Hovering over numbers with units (like "2 km") triggers a tooltip showing conversions (e.g., miles, feet). Clicking opens a context menu with more conversion options, helping users quickly interpret measurements without leaving their current workflow.

- **Selection Tools**: Click to Do supports Freeform, Rectangle, and Ctrl+Click selection modes, allowing users to precisely choose multiple or irregular screen elements. This flexibility enhances usability across different content types and workflows.

- **Privacy and On-Device Processing**: All analysis is performed locally on the device using the NPU, ensuring user privacy. No data is sent to the cloud unless the user initiates a web-based action like "Ask Copilot," "Search the web," or "Visual search with Bing."

## Intended uses

Click to Do can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

- **Government (Public Sector)**: A policy analyst preparing for a high-stakes meeting needs to digest a complex 20-page proposal quickly. Using Click to Do, they highlight a dense section of the document and instantly generate a concise summary or bulleted list of key points. This saves critical time and helps ensure nothing important is overlooked when briefing government officials, improving decision-making under tight deadlines by reducing hours of reading to seconds of AI-assisted insight.

- **Education (K-12)**: A middle school teacher is adapting a text-heavy resource for a class handout. By selecting a challenging paragraph and using Click to Do's rewrite option, the teacher can simplify the language to a more age-appropriate tone. This helps the teacher convey complex information more clearly, making learning materials accessible to students without manually rephrasing each sentence. In another classroom scenario, a science teacher might highlight a metric measurement (e.g., "5 km") and get quick unit conversions (to miles or feet) via Click to Do, ensuring students easily grasp different units in real time and enriching the lesson's clarity.

- **Financial Services (Banking)**: An investment banker is reviewing a scanned contract PDF during a client call. With Click to Do, they can select text directly from the scanned image and copy it to share with colleagues or include in notes – no more retyping complex legal clauses by hand. If the contract also contains an email address (e.g., the client's attorney), the banker can highlight it and click "Send email" or "Schedule meeting with Teams", immediately opening an email draft or calendar invite. By streamlining text extraction and communication in one flow, the banker avoids switching apps and prevents errors, staying focused on the client interaction even in time-sensitive negotiations.

- **Media & Communications (Journalism)**: A news journalist on a tight deadline is researching an international story. They find a relevant statement in French on a foreign news site and use Click to Do to translate the highlighted text into English instantly, grasping its meaning without leaving their screen. They could also use Click to Do to summarize the translated passage into a few bullet points, quickly identifying the newsworthy angles. These features accelerate the research process, allowing the journalist to cover global events faster and with greater accuracy. Similarly, if the reporter receives an image of a press release (instead of text), they can use Click to Do to extract and copy text from the image for quotes, ensuring they get facts right without manual transcription.

- **Consumer Goods (Retail Marketing)**: A marketing specialist at a retail company is quickly editing images for a product catalog update. While previewing a product photo, they launch Click to Do to erase a distracting object from the background with one click, instead of using complex design software. They then blur the background of another product image to make the item stand out and even use background removal to get a clean cut-out of a product for a promotional flyer. These on-screen image actions speed up the content creation process – the specialist accomplishes in seconds what used to require multiple steps in separate editing tools. By minimizing technical hurdles and app-switching, Click to Do helps the marketing team produce polished visuals and copy more efficiently, whether the stakes are a major seasonal campaign or a quick social media post.

## Models and training data

Click to Do leverages a variety of AI models to power the experience that users see. Some examples include Phi Silica (Language Model), Optical Character Recognition (OCR), and Florence Vision Encoder (Image understanding). To learn more about the data used to train the foundation models behind Click to Do, refer to the linked model cards to find the relevant data cards.

## Performance

**Near-Instant Responses**: On Copilot+ PCs with NPUs, Click to Do actions (like text summarization or image text copy) complete in seconds or less, thanks to highly optimized on-device AI models and hardware acceleration.

**Flexible Input & Output**: Users can activate Click to Do via keyboard shortcuts, mouse clicks, or touch, highlighting any text or image on-screen. The tool then offers context-aware actions such as copying, editing, or searching content. Some actions produce immediate results (e.g., text copied to clipboard), while others open a new window or external app for the output (e.g., an email draft, Photos app).

**Accuracy & Limitations**: Click to Do's text recognition is highly accurate for clear on-screen text. However, it works best with English and standard fonts. Advanced AI text actions require English, Spanish, or French content (≥10 words) as well as either a Microsoft account or Microsoft Entra account. Intelligent text actions are powered by the Phi Silica small language model and the NPU, ensuring reliable performance and privacy (all processing occurs locally).

## Limitations

Understanding Click to Do's limitations is crucial to determine it is used within safe and effective boundaries. While we encourage customers to leverage Click to Do in their innovative solutions or applications, it's important to note that Click to Do was not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](/legal/ai-code-of-conduct) (for organizations) or the [Code Conduct section in the Microsoft Services Agreement](https://www.microsoft.com/servicesagreement#3_codeOfConduct) (for individuals) as well as the following considerations when choosing a use case:

- **Hardware-Dependent AI**: Click to Do is only available on a Copilot+ PC or eligible Cloud PC, requiring 40 TOPS NPU (neural processing unit), 16 GB RAM, 8 logical processors, and 256 GB storage capacity.

- **English-Centric Support**: Intelligent text actions work best on English content. If the OS or text is in another language, on-device summarization or rewriting may not be offered, limiting usability in multi-language environments.

- **Input & Content Constraints**: Low-quality images or unusual formats (blurry text, stylized fonts, long form data) can cause OCR failures or inaccurate results, meaning no actionable output for those selections.

## Evaluations

Performance and safety evaluations assess whether AI applications are operating reliably and securely by examining factors like groundedness, relevance, and coherence while identifying the risks of generating harmful content. The following evaluations were conducted with safety components already in place, which are also described in the Safety Components and Mitigations section.

### Performance and quality evaluations

Click to Do was evaluated for performance and safety using a structured, multi-layered approach that aligns with Microsoft's Responsible AI principles and product quality standards. Here's a high-level overview of how the platform was assessed:

- **Scenario-Based Testing**: Click to Do was tested across a range of real-world use cases—such as summarizing text, rewriting content, and extracting information from images—to help ensure responsiveness and reliability under typical user conditions.

- **Hardware-Specific Benchmarks**: The platform's performance was measured on Copilot+ PCs equipped with NPUs, focusing on metrics like time-to-first-token, throughput rate, memory usage, and power efficiency. These benchmarks helped validate that the on-device AI models (e.g., Phi Silica) could deliver fast and efficient results without straining system resources.

- **Content-Type Coverage**: Evaluations included diverse input modalities—text, images, screenshots, and OCR regions—to help ensure consistent performance across different content types. Special attention was given to edge cases like low-resolution images and complex layouts.

- **Language and Regional Testing**: Click to Do's intelligent text actions were tested primarily in English, with structured bug bashes and manual QA conducted for additional supported languages (e.g., Spanish, French) to assess readiness for multilingual support.

- **Telemetry and Engagement Analysis**: Usage data and feedback from Windows Insider Program participants were analyzed to identify performance bottlenecks, user drop-off points, and areas for improvement.

#### Examples of Results

- **Ideal**: A user highlights a well-formatted paragraph of English text from a webpage using Click to Do on a Copilot+ PC. The user selects "Summarize." Within 1–2 seconds, a concise and accurate summary appears in a floating window, capturing the key points of the paragraph. The summary is grammatically correct, contextually relevant, and ready to be copied or inserted into another document.

- **Suboptimal**: A user tries to summarize a blurry scanned image of a printed document in French using Click to Do. The user selects the text in the image and clicks "Summarize." The "Summarize" option is not available. The OCR struggles to recognize the French text due to low image quality and stylized fonts. The user is only offered basic actions like "Copy" or "Search the web," and even those may fail due to poor text extraction.

#### Evaluation data for quality and safety

Our evaluation data is custom-built to assess AI application performance across key areas of safety and quality, simulating real-world scenarios and risks. We begin by identifying relevant evaluation aspects of concern based on multi-disciplinary research and expert input. These concerns are translated into targeted evaluation objectives and guide formulation of evaluation metrics. For safety, we create adversarial prompts to elicit undesirable or edge-case responses, which are then scored using AI-assisted annotators trained to assess alignment with Microsoft's safety standards. For quality, we craft rubric-based prompts relevant to scenarios including evaluating retrieval-augmented generation (RAG) applications and agents. Datasets are curated from diverse sources including synthetic and public datasets to simulate real-world user scenarios. Using the curated datasets, both evaluations undergo iterative refinement and human alignment to improve metric efficacy and reliability. This methodology forms the foundation of repeatable, rigorous assessments that reflect how customers use evaluations to build better and safer AI.

### Custom evaluations

Click to Do's intelligent text actions were tested primarily in English, but included structured, manual evaluation and testing for additional languages (Spanish, French, and more) to assess multilingual support. Collected texts were translated, were transformed by a specific set of Click to Do actions and evaluated for accuracy and relevance.

#### Examples of Results

- **Ideal**: A user highlights a well-formatted paragraph of English text from a webpage using Click to Do on a Copilot+ PC. The user selects "Summarize." Within 1–2 seconds, a concise and accurate summary appears in a floating window, capturing the key points of the paragraph. The summary is grammatically correct, contextually relevant, and ready to be copied or inserted into another document.

- **Suboptimal**: A user tries to summarize a paragraph in Japanese using Click to Do. The user selects the text in the image and clicks "Summarize." The "Summarize" option is not available. The OCR struggles to recognize the Japanese text due to stylized fonts. The user is only offered basic actions like "Copy" or "Search the web," and even those may fail due to poor text extraction.

## Safety components and mitigations

- **Content Moderation and Filtering**: The Phi Silica model was tested for its ability to avoid generating harmful or inappropriate content. Safety alignment techniques, such as post-training moderation and prompt filtering, were applied to reduce risks.

- **Privacy and Data Handling**: All processing for Click to Do actions is performed locally on the device, ensuring that sensitive user data is not transmitted to external servers unless explicitly requested (e.g., for web search or cloud-based Copilot actions). Temporary files created during actions are stored securely and deleted after use.

- **Child Safety Controls**: Click to Do includes safeguards to prevent access to AI features for child accounts, ensuring age-appropriate usage of generative capabilities.

- **Regional Compliance**: Features were adapted or disabled in specific regions to comply with local regulations and privacy laws.

## Best practices for deploying and adopting Click to Do

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

### Deployers and end-users should

- **Exercise caution and monitor outcomes when using Click to Do for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

- **Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

### End-users should

- **Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to the user's intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Click to Do and verify that they match their expectations and requirements.

- **Submit feedback or report issues as they arise**: End-users can submit feedback or report issues direct through the in-product feedback option (e.g., Help > Feedback in Windows Settings) or from within Click to Do itself.

### Deployers should

**Check model availability**: Ensure deployed devices have NPUs to leverage Windows models and that the operating system has up-to-date models, drivers, and firmware. Regular updates help mitigate vulnerabilities and support continued protection and optimal performance.

## Learn more about Click to Do

For additional guidance on the responsible use of Click to Do, we recommend reviewing the following documentation:
- [Click to Do: do more with what's on your screen](https://support.microsoft.com/en-us/windows/click-to-do-do-more-with-what-s-on-your-screen-6848b7d5-7fb0-4c43-b08a-443d6d3f5955)
- [Microsoft Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=521839%E2%80%AF)

### Learn more about responsible AI

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/training/paths/responsible-ai-business-principles/)
