---
title: Application card - Recall
description: Learn about Recall's AI features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 03/24/2026
ms.topic: concept-article
ms.service: windows
---

# Application card: Recall

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform services. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how [enterprise customers](/legal/ai-code-of-conduct) and [individuals](https://www.microsoft.com/servicesagreement) can engage with AI responsibly.

## Overview

Recall (preview) was introduced in 2024, with the ability to enable you to quickly find and jump back into what you have seen before on your PC. You can use an explorable timeline to find the content you remember seeing before. You can also use semantic powered search and just describe how you remember something and Recall will retrieve the moment you saw it. Any photo, link, or message can be a fresh point to continue from.

To use Recall you need to opt in to saving snapshots, which are screenshots of your activity. Snapshots and the contextual information derived from them are saved and encrypted to your local hard drive. Recall does not share snapshots or associated data with Microsoft or third parties, nor is it shared between different Windows users on the same device. Windows will ask for your permission before saving snapshots. You are always in control of what apps and websites get saved in snapshots, and you can delete snapshots, pause or turn them off at any time. Any future options for the user to share data will require fully informed explicit action by the user.

If you opt in to the feature, then as you use your PC, a snapshot of your active screen will be saved every few seconds and when the content of your active window changes. Snapshots are also protected with Windows Hello, so that only the signed in user can access their Recall content. Recall allows you to search for content, including both images and text, using the clues you remember. Trying to remember the name of the sustainable restaurant you saw last week? Just ask Recall and it retrieves both text and visual matches for your search, automatically sorted by how closely the results match your search. Recall even offers you an option to jump back into the content you saw and pick up where you left off.

- [Retrace your steps with Recall - Microsoft Support](https://support.microsoft.com/windows/retrace-your-steps-with-recall-aa03f8a0-a78b-4b3e-b0a1-2eb8ac48701c)
- [Manage Recall for Windows clients | Microsoft Learn](/windows/client-management/manage-recall)
- [Sensitive information filtering in Recall | Microsoft Learn](/windows/ai/recall/sensitive-information-filtering)
- [Recall overview - Windows apps | Microsoft Learn](/windows/ai/recall/)
- [Update on the Recall preview feature for Copilot+ PCs | Windows Experience Blog](https://blogs.windows.com/windowsexperience/2024/06/07/update-on-the-recall-preview-feature-for-copilot-pcs/)
- [Update on Recall security and privacy architecture | Windows Experience Blog](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/)

## Key terms

The following table provides a glossary of key terms related to Recall:

| Term | Definition |
|------|------------|
| **Recall** | Recall allows users to search locally saved and locally analyzed snapshots of their screen using natural language. |
| **Click to Do** | Click to Do runs on top of your snapshots in Recall. Click to Do helps you get things done faster by identifying text and images on your screen that you can take actions with. |
| **Snapshot(s)** | Snapshots are taken periodically while content on the screen is different from the previous snapshot. The snapshots of your screen are organized into a timeline. Snapshots are locally stored and locally analyzed on your PC. |

## Key features or capabilities

The key features and capabilities outlined here describe what Recall is designed to do and how it performs across supported tasks.

- **Review your timeline**: To review your previous snapshots, select the timeline from Recall's navigation bar. Your timeline in Recall is broken up into segments, which are the blocks of time that snapshots of your screen are taken while you were using your PC. You can hover over your timeline to review your activity in a preview window. Selecting the location on the timeline or selecting the preview window loads the snapshot where you can interact with the content.

- **Search with Recall**: Maybe you wanted to make that pizza recipe you saw earlier today but you don't remember where you saw it. Typing *thin crust pizza* into the search box would easily find the recipe again. You could also search for *pizza* if you didn't remember the specific type of pizza. Less specific searches are likely to bring up more matches though. If you prefer to search using your voice, you can select the microphone then speak your search query.

- **Interacting with content with Click to Do**: Once you've found the item you want to see again, select the tile. Recall opens the snapshot and enables Click to Do, which runs on top of the saved snapshot. Click to Do analyzes what's in the snapshot and allows you to interact with images or text that is recognized in the snapshot. You'll notice that when Click to Do is active, your cursor is blue and white. The cursor also changes shape depending on the type of info beneath it. What you can do with the info changes based on what kind of content Click to Do detects. If you select a picture in the snapshot, you can copy, edit with your default picture editing app such as Photos, or open it in another app like the Snipping Tool, or Paint. When you highlight text with Click to Do, you can open it in a text editor or copy it. For example, you might want to copy the text of a recipe's ingredients list to convert it to metric.

- **Jump back into**: Above your selected snapshot, you have more snapshot options. In many cases, you can have Recall jump back into the item, such as reopening the webpage, PowerPoint presentation, or app that was running at the time the snapshot was taken. You can also hide Click to Do, copy the snapshot, or delete the snapshot.

- **Delete, delete all, or copy a snapshot from search results**: If you discover a snapshot that you want to delete or copy, select **…** to display options for the snapshot. You can choose to Copy the snapshot to your clipboard or Delete the snapshot. If you realize that the snapshot contains a website or app that you haven't filtered yet, you have an option to Delete all the snapshots that contain the selected website or app. You will be informed of how many snapshots from that web site or app will be deleted. If you proceed, all the snapshots are deleted in bulk. If you'd like, you can add the website or app to the filter list once the delete is finished.

- **Pause or resume snapshots**: To pause Recall, select the Recall icon in the system tray and select **Pause until tomorrow**. Snapshots will be paused until they automatically resume at 12:00 AM. When snapshots are paused, the Recall system tray icon has a slash through it so you can easily tell that saving snapshots is disabled. To manually resume snapshots, select the Recall icon in the system tray and then select **Resume snapshots**, you will be prompted to authorize this change using your Windows Hello credentials. You can also access the Recall & snapshots settings page from the bottom of this window.

- **Filtering apps, websites, sensitive information from your snapshots**: You can filter out apps and websites from being saved as snapshots. You can add apps and websites at any time by going to **Settings > Privacy & security > Recall & snapshots** on your PC. You'll need to use a supported browser for filtering websites and filtering private browsing activity. For sensitive information, you can use the Sensitive information filtering setting, which is enabled by default, helps filter out snapshots when potentially sensitive information is detected—for example, passwords, credit cards, and more. For more information, see [Filtering apps, websites, and sensitive information in Recall](/windows/ai/recall/sensitive-information-filtering).

## Intended uses

Recall can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

- **Review your timeline**: To review your previous snapshots, select the timeline from Recall's navigation bar. Your timeline in Recall is broken up into segments, which are the blocks of time that snapshots of your screen are taken while you were using your PC. You can hover over your timeline to review your activity in a preview window. Selecting the location on the timeline or selecting the preview window loads the snapshot where you can interact with the content.

- **Search with Recall**: Maybe you wanted to make that pizza recipe you saw earlier today but you don't remember where you saw it. Typing *thin crust pizza* into the search box would easily find the recipe again. You could also search for *pizza* if you didn't remember the specific type of pizza. Less specific searches are likely to bring up more though. If you prefer to search using your voice, you can select the microphone then speak your search query.

- **Jump back into**: Above your selected snapshot, you have more snapshot options. In many cases, you can have Recall jump back into the item, such as reopening the webpage, PowerPoint presentation, or app that was running at the time the snapshot was taken. You can also hide Click to Do, copy the snapshot, or delete the snapshot.

## Models and training data

Recall uses optical character recognition model (OCR), local to the PC, to analyze snapshots and facilitate search. For more information about OCR, see [Transparency note and use cases for OCR](/legal/cognitive-services/computer-vision/ocr-transparency-note). For more information about privacy and security, see [Privacy and control over your Recall experience](https://support.microsoft.com/windows/privacy-and-control-over-your-recall-experience-d404f672-7647-41e5-886c-a3c59680af15).

## Performance

### System requirements for Recall

Your PC needs the following minimum system requirements for Recall:

- A Copilot+ PC that meets the Secured-core standard
- 40 TOPs NPU (neural processing unit)
- 16 GB RAM
- 256 GB storage capacity
- To enable Recall, you'll need at least 50 GB of storage space free
- Saving snapshots automatically pauses once the device has less than 25 GB of storage space
- Users need to enable Device Encryption or BitLocker
- Users need to enroll into Windows Hello Enhanced Sign-in Security with at least one biometric sign-in option enabled in order to authenticate

### Filtering apps, websites, and sensitive information in Recall

You are always in control of what snapshots Recall (preview) can save. You can select which apps and websites you want to filter from your saved snapshots, such as banking apps and websites. You'll need to use a supported browser for filtering websites and filtering private browsing activity. Website filtering only impacts whether the content of foreground tabs may appear in Recall. Filtering does not prevent browsers, internet service providers (ISPs), websites, organizations, or others from knowing that the website was accessed and tracking your activity. Like other Windows apps, such as the Snipping Tool, Recall won't store digital rights management (DRM) content. Recall doesn't record audio or save continuous video. It also doesn't save game video when Game Mode is active on platforms that support it.

Supported browsers, and their capabilities include:

| Browser | Website filtering | Private browsing filtering |
|---------|-------------------|---------------------------|
| Microsoft Edge | ✓ | ✓ |
| Firefox | ✓ | ✓ |
| Opera | ✓ | ✓ |
| Google Chrome | ✓ | ✓ |
| Chromium-based browsers (124 or later) | Not supported | ✓ |

Recall is optimized for select languages: English, Chinese (simplified), French, German, Japanese, and Spanish. Content-based and storage limitations apply. For more information, see [https://aka.ms/copilotpluspcs](https://aka.ms/copilotpluspcs).

## Limitations

Understanding Recall's limitations is crucial to determine it is used within safe and effective boundaries. While we encourage customers to leverage Recall in their innovative solutions or applications, it's important to note that Recall was not designed for every possible scenario. We encourage users to refer to either the [Microsoft Enterprise AI Services Code of Conduct](/legal/ai-code-of-conduct) (for organizations) or the Code of Conduct section in the [Microsoft Services Agreement](https://www.microsoft.com/servicesagreement) (for individuals) as well as the following considerations when choosing a use case:

- Recall can show results related to perceived emotions of people in images. The processes underlying human emotion are complex, and there are cultural, geographical, and individual differences that influence how we may perceive, experience, and express emotions. Results related to the emotions of people in images are based on how they appear, and may not necessarily accurately indicate the internal state of individual people.

- Recall can use contextual cues in the entire image, including people or entities in the background, which is how it can still associate the image with an individual. Biometric data and inferencing aren't used. Any results that identify an individual or infer an individual's emotion aren't the result of processing of the face, such as facial recognition, or other facial inferencing. For example, if an image contains a photo of a popular athlete wearing their team's jersey and their specific number, Recall may still return a result that might identify the athlete based on those contextual cues.

## Evaluations

A Responsible AI Impact Assessment (RAI) was completed, which covered risks, harms and mitigations analysis across our six RAI principles (Fairness, Reliability & Safety, Privacy & Security, Inclusiveness, Transparency, Accountability). A cohesive RAI Learn and Support document was developed for increasing awareness internally, and external facing RAI content was published to drive trust and transparency with our customers.

## Safety components and mitigations

To learn about Recall security and privacy, see [Update on Recall security and privacy architecture | Windows Experience Blog](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/).

## Best practices for integrating and deploying Recall

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

**Deployers and end-users should:**

- **Exercise caution and monitor outcomes when using Recall for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial platforms, government benefits, healthcare, housing, insurance, legal platforms, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial platforms, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, make sure that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

- **Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI platforms and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI platforms or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

**End-users should:**

- If there's something you like, and especially if there's something you don't like about Recall you can submit feedback to Microsoft by selecting **…** then the Feedback icon in Recall. Filing feedback will send data from Recall to Microsoft, including any screenshots that you attach to the feedback.

**Deployers should:**

- To learn about how to manage Recall for Windows clients, see [Manage Recall for Windows clients | Microsoft Learn](/windows/client-management/manage-recall).

## Learn more about Recall

For additional guidance on the responsible use of Recall, we recommend reviewing the following documentation:

- [Update on Recall security and privacy architecture | Windows Experience Blog](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/)
- [Microsoft AI principles](https://www.microsoft.com/ai/principles-and-approach)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft Azure Learning courses on responsible AI](/training/paths/responsible-ai-business-principles/)
