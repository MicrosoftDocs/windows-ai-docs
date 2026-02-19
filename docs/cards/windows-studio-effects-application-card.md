---
title: Application card - Windows Studio Effects
description: Learn about Windows Studio Effects' AI features, capabilities, intended uses, and responsible AI considerations.
author: GrantMeStrength
ms.author: jken
ms.date: 02/19/2026
ms.topic: concept-article
ms.service: windows
---

# Application card: Windows Studio Effects

Microsoft's Application and Platform Cards are intended to help you understand how our AI technology works, the choices application owners can make that influence application performance and behavior, and the importance of considering the whole application, including the technology, the people, and the environment. Application cards are created for AI applications and platform cards are created for AI platform platforms. These resources can support the development or deployment of your own applications and can be shared with users or stakeholders impacted by them.

As part of its commitment to responsible AI, Microsoft adheres to six core principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. These principles are embedded in the Responsible AI Standard, which guides teams in designing, building, and testing AI applications. Application and Platform Cards play a key role in operationalizing these principles by offering transparency around capabilities, intended uses, and limitations. For further insight, readers are encouraged to explore Microsoft's [Responsible AI Transparency Report](https://www.microsoft.com/ai/responsible-ai) and Code of Conduct, which outline how enterprise customers and consumers can engage with AI responsibly.

## Overview

Windows Studio Effects offers a suite of AI-powered features available on select Windows devices equipped with a Neural Processing Unit (NPU). This dedicated hardware accelerator enables real-time enhancements to video calls and recordings by applying effects to the front-facing camera and built-in microphone, as well as external cameras.

By leveraging specialized AI hardware, Studio Effects delivers improved video and audio quality, extended battery life, and a smoother overall experience. Effects include, for example, Background Blur, Eye Contact, Automatic framing, Voice Focus, Portrait Light, Portrait Blur, Creative Filters, and Eye Contact Teleprompter. These features are designed to provide a polished and professional presence during virtual meetings. These capabilities also support privacy by minimizing distractions in a user's environment.

Studio Effects integrates seamlessly with applications such as Microsoft Teams and is managed through Camera page in the Settings App and Quick Settings in the application tray, allowing users to enable or disable effects with ease. It supports multiple effects simultaneously and is available to individual users, enterprise customers, and developers who want to incorporate these features into their applications. Updates and new capabilities are delivered through regular Windows updates.

## Key terms

| Term | Definition |
|------|------------|
| **AI Accelerator** | Specialized hardware that speeds up AI computations, improving efficiency for video and audio effects. |
| **API (Application Programming Interface)** | A set of rules and tools that allow different software applications to communicate and work together. |
| **Automatic framing** | A feature that keeps the user's face centered and in focus during video calls, even if the user moves around. |
| **Background Blur** | An effect that blurs the background in a video call to help maintain privacy and keep the focus on the user. |
| **CPU (Central Processing Unit)** | The main processor in a computer, used for general computing tasks and, in this context, for video/audio processing if no AI accelerator is available. |
| **Copilot+ PC** | A Windows 11 device equipped with an NPU and enhanced AI features like Recall, Studio Effects, and Live Captions. |
| **DDI (Device Driver Interface)** | A standard way for applications to access hardware features, such as camera effects. |
| **Enterprise Customer** | Any parties who have entered into contracts with Microsoft to license, subscribe and/or use Microsoft applications or platform services. |
| **Facial Landmarks** | Key points on a face (such as eyes, nose, mouth) detected by AI to enable effects like gaze correction and framing. |
| **GPU (Graphics Processing Unit)** | A processor specialized for rendering graphics and handling complex computations, sometimes used for video and audio processing. |
| **IHV (Independent Hardware Vendor)** | Companies that produce hardware components, such as Intel, AMD, or Qualcomm that support MEP features. |
| **NPU** | An NPU (Neural Processing Unit) is a specialized chip that accelerates AI tasks on-device. |
| **OEM (Original Equipment Manufacturer)** | Companies that build devices (like laptops or tablets) and integrate MEP into their products via Windows images or updates. |
| **Opt-in** | A feature or setting that users or OEMs must actively choose to enable; effects in MEP are opt-in by default. |
| **Private Preview / Public Preview / Generally Available (GA)** | Stages in the product lifecycle: Private Preview (limited release), Public Preview (broader release), GA (fully released to all customers). |
| **Qualitative Evaluation** | Assessment based on subjective measures, such as user feedback or manual testing, rather than numerical data. |
| **Service Tree ID** | An identifier used to track services within Microsoft's infrastructure. |
| **Feature** | A specific capability or function provided by the product, such as background blur, Voice Focus, or Automatic framing. |
| **Voice Focus** | A feature that uses AI to suppress background noise and enhance the clarity of the user's voice during calls. |
| **Windows OEM Image** | A customized version of Windows provided by device manufacturers, which includes MEP features. |
| **APO (Audio Processing Object)** | A software component that processes audio signals, allowing apps to use audio effects from MEP. |
| **GUG Dataset** | A dataset used for fairness and quality testing of face detection and landmark models; includes diverse images for evaluating model performance across demographics. |
| **Silbo** | A whistling language mentioned as a unique case for audio processing and fairness evaluation. |

## Key features or capabilities

The key features and capabilities below describe what Studio Effects is designed to do and how it performs across supported tasks.

| Feature | Description | Modes / Variants | Availability |
|---------|-------------|------------------|--------------|
| **Automatic framing** | Keeps you centered in the frame during video calls, even as you move. | – | All NPU-enabled devices |
| **Portrait Light** | Brightens your face to improve visibility in low-light environments. | – | Snapdragon Copilot+ PCs only |
| **Eye Contact** | Adjusts your gaze to simulate natural eye contact during video calls. | **Standard**: Subtle correction to adjust your gaze on video calls to simulate eye contact.<br>**Teleprompter**: Uses advanced AI to create a more natural and lifelike eye contact experience. It maintains natural eye contact even as your eyes move while reading. | **Standard**: All NPU-enabled<br>**Teleprompter**: Snapdragon Copilot+ PCs only |
| **Background Effects** | Blurs or stylizes your background to maintain privacy and focus. | **Standard Blur**: Soft blur<br>**Portrait Blur**: Camera-style depth blur | All NPU-enabled devices |
| **Creative Filters** | Adds artistic effects to your video feed for expressive or fun visuals. | **Illustrated**: Turns your video into an artistic illustration.<br>**Animated**: Adds lively, animated effects to your video.<br>**Watercolor**: Applies a watercolor painting style to your video. | Snapdragon Copilot+ PCs only |
| **Voice Focus** | Isolates your voice and reduces background noise for clearer audio. | – | All NPU-enabled devices with more than 40TOPS |

> [!NOTE]
> Only the following effects are available on devices with a neural processing unit (NPU) that can perform less than 40 trillion operations per second (TOPS):
> - Background effects
> - Eye contact - standard
> - Automatic framing

## Intended uses

Windows Studio Effects can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

- **Automatic Framing**: A virtual personal trainer is demonstrating an exercise at the gym. During the video call, Automatic Framing keeps the trainer centered in the shot as they move between machines so the client can always see the trainer.

- **Voice Focus**: A podcaster joins a recording from a busy airport. Voice Focus isolates their voice from background noise, so their words are clearly heard.

- **Eye Contact**: An executive is presenting to their team and referencing notes off-screen. Eye Contact helps them appear confident and engaged on video even when the executive is glancing at their notes.

- **Portrait Light**: A candidate is completing a virtual interview in a poorly lit environment. Portrait Light assures their face is well-lit and professional, despite the room's poor lighting.

- **Background Effects**: An employee uses a branded background in virtual calls to celebrate their organization's 50th anniversary.

- **Creative Filters**: A mother wants to host a virtual birthday party for their daughter who lives in another state. The mother uses Creative Filters for guests to add playful effects to their video feeds.

## Models and training data

Studio Effects leverages a variety of proprietary AI models to power the experience that users see.

Studio Effects is powered by a series of on-device Computer Vision Models that are owned and maintained by Microsoft. These models use traditional Computer Vision Techniques to apply the effect to your camera stream. These models have been rigorously tested for performance and quality in a variety of scenarios.

## Performance

For Studio Effects to achieve the best performance, the application needs a clear view of the user. This means the person should stand out from the background. Clothing, hair, and skin tone should contrast with the surroundings so the application can easily identify the user. Good lighting is essential. The user's face should be well-lit so that facial features are visible and defined. Poor lighting can make it harder for the application to recognize and track the person accurately.

The application works best when it can follow the main subject, even if they move moderately. Studio Effects is designed to detect and track the user reliably, so normal movements won't disrupt performance. For audio, the user's voice should be clear and consistent, with minimal background noise. Speaking at a steady volume helps the application process sound correctly.

Finally, the platform is optimized for Windows devices that include NPUs or similar hardware accelerators. These components are specialized chips that speed up AI-related tasks, making the application more responsive and efficient.

Studio Effects have the following intended inputs and expected outputs:

### Inputs

- **Video**: Captured from the device camera to enable features such as background blur, eye contact correction, auto-framing, portrait lighting, and creative filters.
- **Audio**: Captured via the microphone for noise suppression using Voice Focus.
- **Images**: Applied indirectly when creative filters process video frames.

### Outputs

- **Video**: A processed video stream with selected visual enhancements applied.
- **Audio**: Speech with reduced background noise for improved clarity.

## Limitations

Despite its optimizations, Windows Studio Effects is subject to several common limitations that can affect performance and user experience across all features. Understanding Studio Effect's limitations is crucial to determine if it is used within safe and effective boundaries, users should consider these limitations when choosing a use case:

### Visual Limitations

- **Poor Lighting or Visual Clarity**: When lighting is too dim, too bright, or uneven, the application struggles to separate the user from the background. Shadows or blurry backgrounds can cause misidentification, incomplete blur, or even blur objects you're holding. For best results, use well-lit spaces with a clear contrast between the user and their surroundings.

- **Subject and Background Blending**: If the user's clothing, hair, or skin tone closely matches the background, the application may not distinguish them properly. This can lead to excessive or incomplete blurring or even misidentification. Users should choose backgrounds that contrast with their appearance to avoid these issues.

- **Detection and Tracking Failures**: Rapid movement, obstructions, or sitting too far from the camera can cause framing errors or distortions. In large rooms or crowded scenes, other people may be incorrectly included or excluded. Unique facial features can also reduce accuracy for effects like eye contact correction.

- **Visual and Identity Distortion**: Some effects may unintentionally alter facial features, eye shape, or color—especially if the user wears glasses or has reflective surfaces nearby. Eye gaze correction may look unnatural in these scenarios.

- **Hardware and Performance Constraints**: If the user's device is under heavy load or overheating, they may notice delays, jitters, or reduced video quality. To prevent this, the user should check if their device is operating within normal temperature and performance limits.

### Audio Limitations

- **Audio and Voice Processing Issues**: Excessive background noise, side conversations, or a very soft voice can confuse the application. It may suppress the wrong sounds or fail to focus on the speaker, making speech hard to understand or mismatched with lip movements. Rare sounds or underrepresented languages may also be suppressed.

### Why Performance Degrades

Visual and Audio Conditions: AI models need clear visual and audio input. Poor lighting, background blending, rapid movement, or noisy environments reduce accuracy. Hardware limitations can further impact real-time processing, causing lag or degraded quality.

## Evaluations

Performance and safety evaluations assess whether AI applications operate reliably and securely by evaluating factors like quality of platform across demographic groups. The following evaluations were conducted with safety components already in place, which are described in the Safety Components & Mitigations section.

### Performance & quality evaluations

#### What We Evaluated

Windows Studio Effects includes video and audio features such as Background Blur, Enhanced Eye Contact, Automatic Framing, Portrait Light, Creative Filters, and Voice Focus. Each feature leverages AI models running on device hardware (NPUs) to deliver real-time effects for video conferencing and camera applications.

#### How We Tested

**Performance**:
- Real-time operation and responsiveness were measured across supported devices, focusing on latency, smoothness, and resource usage.
- Device temperature and battery consumption were monitored for efficient operation.

**Quality**:
- Visual fidelity was assessed for each effect (e.g., naturalness of blur, lighting, and stylization).
- Audio clarity and noise suppression were evaluated for Voice Focus.
- The dataset was selected to balance sensitive groups according to categories defined in RAI documentation and the styles themselves were carefully designed to not prefer certain types of faces or match any perceived beauty standards. The dataset creation approach (input faces and matching portraits) were reviewed by the legal team and approved. The testing uses FID which measures how well the resulting distribution matches the ground truth distribution.

**Fairness**:
- Features were tested on diverse datasets to check for consistent performance across age, gender, ethnicity, and skin tone.
- Quantitative fairness analysis was performed for face detection, landmark, and segmentation models, with metrics tracked for demographic parity.
- For Creative Filter, a dataset of over 1,000 original artist-created portraits was used for style inclusivity.

**Modalities Assessed**:
- **Visual Effects**: Filters and enhancements applied to images or video.
- **Audio**: Background noise suppression and clarity during calls or recordings.
- **Device Performance**: Heat management and power consumption during effect processing.

#### Examples of Results

- **Ideal Outcome**: Effects appear instantly, look natural, and maintain smooth edges with realistic lighting. Audio remains clear with effective noise suppression. Users experience consistent quality regardless of lighting, facial features, or environment. The device stays cool and power-efficient.

- **Suboptimal Outcome**: Glitches such as jagged blur edges, misaligned eyes, or noticeable delays. Audio distortion or uneven performance in low-light or unique facial conditions. Device overheating or excessive battery drain. Conflicts between application and app-level effects, or filters so subtle that users don't realize they're active.

### Risk & safety evaluations

#### How We Tested

**Responsible AI & Fairness**:
- Used diverse datasets and both qualitative and quantitative analysis to check for demographic parity.
- For Enhanced Eye Contact and Portrait Light, accuracy and quality metrics were tracked across sensitive features (age, gender, race).

**Safety & Accessibility**:
- Assessed comfort and accuracy, especially for users with unique traits (e.g., nystagmus, facial obstructions).
- Assured features like Eye Contact and Teleprompter do not misrepresent users or cause discomfort.

**Robustness**:
- Evaluated under edge cases (e.g., low light, noisy rooms, older hardware).

**Hardware Safety**:
- Monitored device temperature and power consumption.

**Modalities assessed**:
- **Video Features**: Background Blur, Enhanced Eye Contact, Automatic Framing, Portrait Light, Creative Filters.
- **Audio Features**: Voice Focus.
- **Fairness & Safety**: Checked that features do not introduce bias or unfair treatment. Fairness metrics were tracked for face detection, landmark, and segmentation models; further analysis for some features is ongoing.
- **Robustness**: Tested in varied environments (lighting, noise, hardware).
- **Hardware Safety**: Verified smooth operation on AI-accelerated devices without overheating or excessive battery drain.

#### Examples of results

- **Ideal Outcome**: Effects work smoothly across different lighting conditions and user demographics. Voice Focus removes unwanted noise while keeping speech clear. Creative Filters apply styles transparently and without bias.

- **Suboptimal Outcome**: Effects distort or behave inconsistently. For example, Eye Contact misaligns or filters look different on certain skin tones. Other poor outcomes include device overheating, rapid battery drain, or users being confused about which effects are active.

#### Evaluation data for quality and safety

Custom datasets were designed to simulate real-world scenarios and potential risks, ensuring evaluations reflected practical conditions. The objectives and metrics for these evaluations were informed by multi-disciplinary research and expert input, providing a comprehensive approach to assessing performance. Fairness and reliability were key priorities, with metrics tracked and improved over time—for example, face detection error rates were reduced and segmentation model fairness was assessed. Known limitations and failure modes were carefully documented, and mitigation strategies were planned, such as collecting additional data for low-contrast scenarios or conducting user studies to address unique traits.

## Safety components & mitigations

**Cybersecurity Measures**: Camera and microphone permissions are enforced at the operating application level. Customers can take screen shots of their video stream and record themselves via voice recorder and camera app while using Studio Effects.

## Best practices for integrating and deploying Studio Effects

Responsible AI is a shared commitment between Microsoft and its customers. While Microsoft builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

### End users and deployers should

- **Exercise caution and monitor outcomes when using Studio Effects for consequential decisions or in sensitive domains**: Consequential decisions are those that may have a legal or significant impact on a person's access to education, employment, financial services, government benefits, healthcare, housing, insurance, legal services, or that could result in physical, psychological, or financial harm. Sensitive domains—such as financial services, healthcare, and housing—require particular care due to the potential for disproportionate impact on different groups of people. When using AI for decisions in these areas, communicate effectively so that impacted stakeholders can understand how decisions are made, appeal decisions, and update any relevant input data.

- **Evaluate legal and regulatory considerations**: Customers need to evaluate potential specific legal and regulatory obligations when using any AI services and solutions, which may not be appropriate for use in every industry or scenario. Additionally, AI services or solutions are not designed for and may not be used in ways prohibited in applicable terms of service and relevant codes of conduct.

### End-users should

**Monitor and detect performance drift by**:
- **Conducting visual checks**: Look for artifacts such as unnatural blur edges, eye misalignment, or inconsistent lighting.
- **Conducting audio checks**: Monitor for distortion or loss of speech clarity when Voice Focus is enabled.
- **Tracking Application Performance**: Use built-in diagnostics or Task Manager to track CPU/GPU/NPU load.
- **Updating Regularly**: Apply OS and driver updates to maintain model accuracy and fairness.

**Submit feedback or report issues as they arise**: End-users can submit feedback or report issues directly through the in-product feedback option (e.g., Help > Feedback in Windows Settings) or via the official support portal. For urgent security or privacy concerns, use the dedicated security reporting channel provided by Microsoft.

### Deployers should

**Check application maintenance and security by**:
- **Maintaining current software**: Confirming all deployed devices have up-to-date operating systems, drivers, and firmware. Regular updates help mitigate security vulnerabilities and supports continued protection and optimal performance.
- **Using trusted software sources**: Only install applications, plug-ins, and updates from verified sources. Avoid unverified or third-party components, as these may introduce security risks or operational conflicts.
- **Reviewing permission policies**: Before deployment, validate that camera and microphone permissions are correctly configured. Proper configuration helps prevent unauthorized access and protects user privacy.
- **Enabling enterprise-grade security**: Activate features such as Windows Hello for secure authentication and BitLocker for data encryption to enhance device security in production environments.

**Optimize the platform for end-users by**:
- **Configuring NPU Acceleration**: Enable Neural Processing Unit (NPU) acceleration by default to support low-latency, AI-driven effects.
- **Preconfiguring recommended effects**: Set default effects based on usage scenarios to streamline user experience:
  - Professional calls: Background Blur and Eye Contact
  - Content creation: Portrait Light and Creative Filters.
- **Calibrating for accessibility and usability**: Calibrate camera and lighting profiles to ensure natural, professional results across environments.

**Conduct pre-deployment testing for performance and safety by**:
- **Validating across use cases**: Test application performance in low-light environments, high-noise conditions, and accessibility scenarios to confirm reliable operation for diverse user needs.

**Offer support for troubleshooting and ongoing maintenance by**:
- **Addressing visual or audio Issues**: If users encounter artifacts, misalignment, or voice problems, reset effect settings, adjust microphone sensitivity, or temporarily disable conflicting applications or features.
- **Monitoring performance**: Use built-in diagnostics to track CPU, GPU, and NPU usage. Encourage users to report anomalies for continuous improvement.
- **Applying regular updates**: Keep firmware and drivers current to maintain application accuracy, fairness, and security.

## Learn more about Studio Effects

For additional guidance on the responsible use of Windows Studio Effects we recommend reviewing the existing documentation: [Windows Studio Effects Overview](/windows/apps/develop/windows-integration/studio-effects).

### Learn more about responsible AI

- [Microsoft AI principles](https://www.microsoft.com/ai/responsible-ai)
- [Microsoft responsible AI resources](https://www.microsoft.com/ai/responsible-ai-resources)
- [Microsoft Azure Learning courses on responsible AI](/training/paths/responsible-ai-business-principles/)
