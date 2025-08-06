---
title: Responsible Generative AI Development on Windows
description: Guidance for applying Responsible AI principles and practices in a Windows development context.
ms.author: mattwoj
author: mattwojo
ms.date: 08/05/2025
ms.topic: overview
---

# Developing Responsible Generative AI Applications and Features on Windows

This document provides an overview of recommended responsible development practices to use as you create applications and features on Windows with generative artificial intelligence.

**[Windows AI Foundry](./overview.md)** on-device generative AI models can help you to enforce local content safety features, such as on-device classification engines for harmful content and a default blocklist. Microsoft prioritizes supporting developers to build safe, trustworthy AI experiences with local models on Windows.

## Guidelines for responsible development of generative AI apps and features on Windows

Every team at Microsoft follows [core principles and practices](https://www.microsoft.com/en-us/ai/responsible-ai) to responsibly build and ship AI, including Windows. You can read more about Microsoft's approach to responsible development in the [Microsoft Responsible AI Transparency Report](https://www.microsoft.com/corporate-responsibility/responsible-ai-transparency-report). Windows follows foundational pillars of RAI development — govern, map, measure, and manage — that are aligned to the National Institute for Standards and Technology (NIST) AI Risk Management Framework.

## Govern - Policies, practices, and processes

Standards are the foundation of governance and compliance processes. Microsoft has developed our own [Responsible AI Standard,](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE5cmFl) including [six principles](https://www.microsoft.com/ai/principles-and-approach) that you can use as a starting point to develop your guidelines for responsible AI. We recommend you build AI principles into your development lifecycle end to end, as well as into your processes and workflows for compliance with laws and regulations across privacy, security, and responsible AI. This spans from early assessment of each AI feature, using tools like the [AI Fairness Checklist](https://www.microsoft.com/research/project/ai-fairness-checklist/#overview) and [Guidelines for Human-AI Interaction - Microsoft Research,](https://www.microsoft.com/research/project/guidelines-for-human-ai-interaction/) to monitoring and review of AI benchmarks, testing and processes using tools like a [Responsible AI scorecard](/azure/machine-learning/concept-responsible-ai-scorecard), to public documentation into your AI features' capabilities and limitations and user disclosure and controls -- notice, consent, data
collection and processing information, etc. -- in keeping with applicable privacy laws, regulatory requirements, and policies.

## Map - Identify risk

Recommended practices for identifying risks include:

### End-to-end testing

End-to-end testing evaluates the entire AI system from start to finish to ensure that it operates as intended and adheres to established standards. This comprehensive approach may include:

#### Red teaming

The term [red teaming](/azure/ai-services/openai/concepts/red-teaming) has historically described systematic adversarial attacks for testing security vulnerabilities. More recently, the term has extended beyond traditional cybersecurity and evolved in common usage to describe many kinds of probing, testing, and attacking of AI systems.

With both large language models (LLMs) and small language models (SLMs), both benign and adversarial usage may produce potentially harmful outputs that can take many forms, including hate speech, incitement or glorification of violence, or sexual content. Thorough red teaming allows you to stress-test your system and refine your content strategy to decrease the possibility that your system causes harm.  

All AI systems should undergo red team testing, depending on function and purpose, for both high-risk systems that employ generative AI and lower-risk systems that use non-generative AI:

- **Formal red teaming**: Independent red teaming should be completed for all high-risk systems that employ generative AI using large language models (LLMs). Formal red teaming includes recruiting professionals outside of your organization to participate in red teaming activities.

- **Internal red teaming**:  At a minimum, plan internal red teaming for all lower-risk, non-generative AI systems. This can be done by people inside your organization.  

Learn more about red teaming and how to assess your system's red teaming needs: [Microsoft AI Red Team](/security/ai-red-team/)

#### Model evaluation

As a part of end-to-end testing, it is important to evaluate the model itself.

- **Model Card**: For publicly available models, such as those on HuggingFace, you can check each model's Model Card as a handy reference to understand if a model is the right one for your use case. [Read more about Model Cards](https://huggingface.co/docs/hub/model-cards).

- **Manual testing**: Humans performing step-by-step tests without scripts is an important component of model evaluation that supports...

  - Measuring progress on a small set of priority issues. When mitigating specific harms, it's often most productive to keep manually checking progress against a small dataset until the harm is no longer observed before moving to automated measurement.

  - Defining and reporting metrics until automated measurement is reliable enough to use alone.

  - Spot-checking periodically to measure the quality of automatic measurement.

- **Automated testing**: Automatically executed testing is also an important component of model evaluation that supports...

  - Measuring at a large scale with increased coverage to provide
    more comprehensive results.

  - Ongoing measurement to monitor for any regression as the system,
    usage, and mitigations evolve.

- **Model selection:** Select a model that is suited for your purpose and educate yourself to understand its capabilities, limitations, and potential safety challenges. When testing your model, make sure that it produces results appropriate for your use. To get you started, destinations for Microsoft (and non-Microsoft/open source) model sources include:

  - [Hugging Face](https://huggingface.co/)

  - [ONNX Model Zoo](https://onnx.ai/models/)

  - [Qualcomm AI Hub](https://aihub.qualcomm.com/)

  - [AI Toolkit for VS Code](./toolkit/index.md)

  - [PyTorch Hub](https://pytorch.org/hub/)

  - [Tensorflow Hub](https://www.tensorflow.org/hub)

## Measure - Assess risks and mitigation

Recommended practices include:

- **Assign a Content Moderator**: Content Moderator checks text, image, and video content for material that is potentially offensive, risky, or otherwise undesirable in content.
 Learn more: [Introduction to Content Moderator (Microsoft Learn Training)](/training/modules/intro-to-content-moderator/).

  - **Use content safety filters**: This ensemble of multi-class classification models detects four categories of harmful content (violence, hate, sexual, and self-harm) at various severity levels (low, medium, and high). Learn more: [How to configure content filters with Azure OpenAI Service](/azure/ai-services/openai/how-to/content-filters).

  - **Apply a meta-prompt:** A meta-prompt is a system message included at the beginning of the prompt and is used to prime the model with context, instructions, or other information relevant to your use case. These instructions are used to guide the model's behavior. Learn more: [Creating effective security guardrails with metaprompt / system message engineering](https://techcommunity.microsoft.com/t5/ai-machine-learning-blog/marchresponsibly-creating-effective-security-guardrails-with/ba-p/4099284).

  - **Utilize blocklists:** This blocks the use of certain terms or patterns in a prompt. Learn more: [Use a blocklist in Azure OpenAI](/azure/ai-services/openai/how-to/use-blocklists).

  - **Get familiar with the provenance of the model**: Provenance is the history of ownership of a model, or the who-what-where-when, and is very important to understand. Who collected the data in a model? Who does the data pertain to? What kind of data is used? Where was the data collected? When was the data collected? Knowing where model data came from can help you assess its quality, reliability, and avoid any unethical, unfair, biased, or inaccurate data use.

  - **Use a standard pipeline**: Use one content moderation pipeline rather than pulling together parts piecemeal. Learn more: [What are Azure Machine Learning pipelines?](/azure/machine-learning/concept-ml-pipelines).

- **Apply** **UI** **mitigations:** These provide important clarity to your user about capabilities and limitations of an AI-based feature. To help users and provide transparency about your feature, you can:

  - Encourage users to edit outputs before accepting them

  - Highlight potential inaccuracies in AI outputs

  - Disclose AI's role in the interaction

  - Cite references and sources

  - Limit length of input and output where appropriate

  - Provide structure out input or output – prompts must follow a standard format

  - Prepare pre-determined responses for controversial prompts.

- **Implement customer feedback loops:** Encourage your users to actively engage in feedback loops:

  - Ask for feedback directly in your app / product using a simple feedback mechanism that is available in context as part of the user experience.

  - Apply social listening techniques on the channels your customers use for early conversations about feature issues, concerns, and possible harm.

## Manage - Mitigate AI risks

Recommendations for mitigating AI risks include:

- **Abuse monitoring:** This methodology detects and mitigates instances of recurring content and/or behaviors that suggest a service has been used in a manner that may violate the Code of Conduct or other applicable product terms. Learn more: [Abuse Monitoring](/azure/ai-services/openai/concepts/abuse-monitoring).

- **Phased delivery**: Roll out your AI solution slowly to handle incoming reports and concerns.

- **Incident response plan**: For every high-priority risk, evaluate what will happen and how long it will take to respond to an incident, and what the response process will look like.

- **Ability to turn feature or system off**: Provide functionality to turn the feature off if an incident is about to or has occurred that requires pausing the functionality to avoid further harm.

- **User access controls/blocking**: Develop a way to block users who are misusing a system.

- **User feedback**: Utilize mechanisms to detect issues from the user's side.

  - Ask for feedback directly in your product, with a simple feedback mechanism that is available in the context of a typical workflow.

  - Apply social listening techniques on the channels your customers use for early conversations about feature issues, concerns, and possible harm.

- **Responsible deployment of telemetry data**: Identify, collect, and monitor signals that indicate user satisfaction or their ability to use the system as intended, ensuring you follow applicable privacy laws, policies, and commitments. Use telemetry data to identify gaps and improve the system.

## Tools and resources

- [**Windows AI Foundry**](./overview.md): A unified, reliable and secure platform supporting the AI developer lifecycle from model selection, finetuning, optimizing and deployment across CPU, GPU, NPU and cloud.

- [**Responsible AI Toolbox**](https://github.com/microsoft/responsible-ai-toolbox): Responsible AI is an approach to assessing, developing, and deploying AI systems in a safe, trustworthy and ethical manner. The Responsible AI  toolbox is a suite of tools providing a collection of model and data exploration and assessment user interfaces and libraries that enable a better understanding of AI systems. These interfaces and libraries empower developers and stakeholders of AI systems to develop and monitor AI more responsibly and take better data-driven actions.

- [**Responsible AI Dashboard Model Debugging**](https://responsibleaitoolbox.ai/introducing-responsible-ai-dashboard/): This dashboard can help you to Identify, Diagnose, and Mitigate issues, using data to take informed actions. This customizable experience can be taken in a multitude of directions, from analyzing the model or data holistically, to conducting a deep dive or comparison on cohorts of interest, to explaining and perturbing model predictions for individual instances, and to informing users on business decisions and actions. [Take the Responsible AI Decision Making Quiz](https://responsibleaitoolbox.ai/quiz-page-2/).

- Review the Azure Machine Learning summary of [What is Responsible AI?](/azure/machine-learning/concept-responsible-ai)

- Read the [Approach to Responsible AI for Copilot in Bing](https://support.microsoft.com/topic/copilot-in-bing-our-approach-to-responsible-ai-45b5eae8-7466-43e1-ae98-b48f8ff8fd44).

- Read Brad Smith's article on [Combating abusive AI-generated content: a comprehensive approach](https://blogs.microsoft.com/on-the-issues/2024/02/13/generative-ai-content-abuse-online-safety/) from Feb 13, 2024.

- Read the [Microsoft Security Blog](https://www.microsoft.com/security/blog/).

- [Overview of Responsible AI practices for Azure OpenAI models - Azure AI services](/legal/cognitive-services/openai/overview)

- [How to use content filters (preview) with Azure OpenAI Service](/azure/ai-services/openai/how-to/content-filters)

- [How to use blocklists with Azure OpenAI Service](/azure/ai-services/openai/how-to/use-blocklists)

- [Planning red teaming for large language models (LLMs) and their applications](/azure/ai-services/openai/concepts/red-teaming)

- [Azure OpenAI Service abuse monitoring](/azure/ai-services/openai/concepts/abuse-monitoring)

- [Threat modeling AI/ML systems and dependencies](/security/engineering/threat-modeling-aiml)

- [AI/ML pivots to the security. A development lifecycle bug bar](/security/engineering/bug-bar-aiml)

- [Failure modes in machine learning](/security/engineering/failure-modes-in-machine-learning)

- [Tools for Managing and Ideating Responsible AI Mitigations - Microsoft Research](https://www.microsoft.com/research/project/tools-for-managing-and-ideating-responsible-ai-mitigations/)

- [Planning for natural language failures with the AI Playbook](https://www.microsoft.com/research/publication/planning-for-natural-language-failures-with-the-ai-playbook/)

- [Software engineering for ML: A case study](https://www.microsoft.com/research/publication/software-engineering-for-machine-learning-a-case-study/)

- [Security and machine learning in the real world](https://arxiv.org/abs/2007.07205)

- [Overreliance on AI: Literature review](https://www.microsoft.com/research/publication/overreliance-on-ai-literature-review/)

- [Error Analysis](https://erroranalysis.ai/) and [Build Responsible AI using Error Analysis toolkit (youtube.com)](https://www.youtube.com/watch?v=kIOZOYZ8_es&t=3s)

- [InterpretML](https://interpret.ml/) and [How to Explain Models with IntepretML Deep Dive (youtube.com)](https://www.youtube.com/watch?v=WwBeKMQ0-I8&t=5s)

- [Black-Box and Glass-Box Explanation in Machine Learning (youtube.com)](https://www.youtube.com/watch?v=7uzNKY8pEhQ)
