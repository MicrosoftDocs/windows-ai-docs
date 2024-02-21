---
title: Important API concepts
description: Learn about important concepts for the Windows Vision Skills API. This API is meant to streamline the way skills work and how developers interact with them.
ms.date: 01/09/2024
ms.topic: article
keywords: windows 10, windows ai, windows vision skills
---

# Important API concepts

> [!NOTE]
> The Microsoft.AI.Skills.SkillInterfacePreview namespace has been deprecated as it is legacy and is no longer maintained. It has been replaced by non-monikored package offering the same functionalities and more with improve performance and reliability: [Microsoft.AI.Skills.SkillInterface](https://www.nuget.org/packages/Microsoft.AI.Skills.SkillInterface/). This documentation is scheduled to be updated to reflect the new functionality.

The Microsoft.AI.Skills.SkillInterfacePreview namespace provides a set of base interfaces to be extended by all skills as well as classes and helper methods for skill implementers on Windows.

This API is meant to streamline the way skills work and how developers interact with them. The goal is to abstract away the API specificities of each skill's logic and leverage instead a consistent developer intuition over a broad set of solutions and applications. It is also meant to leverage Windows primitives which eases interop with OS APIs. This constitutes a template to derive from that standardizes the developer flow and API interactions across all skills.

Therefore all skills are expected to implement at the very least the following interfaces:

<br/>

| Name | Description |
|------|-------------|
| ISkillDescriptor| Provides information on the skill and exposes its requirements (input, output, version, etc). Acts as a factory for the ISkill. |
| ISkillBinding | Serves as an indexed container of ISkillFeature. It acts as a conduit for input and output variables to be passed back and forth to the ISkill. It handles pre-processing and post-processing of the input/output data and simplifies their access. |
| ISkill | Exposes the core logic of processing its input and forming its output via an ISkillBinding. Acts as a factory for the ISkillBinding. |

Skills are meant to optimally leverage the hardware capabilities (CPU, GPU, etc.) of each system they run on. These hardware acceleration devices are represented by deriving the ISkillExecutionDevice interface associated with a SkillExecutionDeviceKind. Therefore, ISkillDescriptor derivatives have to find, filter, and expose the set of ISkillExecutionDevice objects currently available on the host system that can execute the ISkill logic.

This will allow the developer consuming the skill package to best chose how to tap into supported hardware resources at runtime on a user's system. There are some ISkillExecutionDevice derivative classes already defined and available for convenience SkillExecutionDeviceCPU and SkillExecutionDeviceDirectX).

In order to ensure that input and output variables are passed in the correct format to the skill, the ISkillFeature interface is defined. It is meant to encapsulate a value in a predefined format. This value is represented by a derivative of ISkillFeatureValue, which has multiple common derivatives already defined and available for convenience ISkillFeatureTensorValue, SkillFeatureImageValue, and SkillFeatureMapValue).

Each ISkillFeatureValue derivative has a corresponding ISkillFeatureDescriptor derivative defined that describes the expected format supported by the skill (ISkillFeatureTensorDescriptor, ISkillFeatureImageDescriptor, and ISkillFeatureMapDescriptor). These ISkillFeatureDescriptor derivatives also act as factory objects for the ISkillFeatureValue objects they describe.

At instantiation time, if the primitive used to assign to ISkillFeatureValue does not match the description provided by its ISkillFeatureDescriptor counterpart, an automatic conversion specific to each type can occur seamlessly (for example, transcoding when binding an image in a different format than the one required, or cropping if the aspect ratio differs).

## API flow <a name="APIFlow"></a>

Since all skills derive the same set of base interfaces from Microsoft.AI.Skills.SkillInterfacePreview, they all follow the same flow which usually boils down to the following operations:



![Windows Vision Skill API flow diagram showcasing the usual sequence of API calls from an application ingesting a Windows Vision Skill](../images/vision-skills-flow.png)



1) Instantiate the ISkillDescriptor derivative.

2) Query the available execution devices using the instance from step 1 (using ISkillDescriptor.GetSupportedExecutionDevicesAsync) or skip and directly attempt step 3.

3) Instantiate the skill using the instance from step 1 and the desired execution device from step 2 (using ISkillDescriptor.CreateSkillAsync(ISkillExecutionDevice)) or the default one decided by the skill developer (using ISkillDescriptor.CreateAsync()).

4) Instantiate a skill binding object using the skill instance from step 3 (using ISkill.CreateSkillBindingAsync).

5) Retrieve your primitives ([VideoFrame](/uwp/api/windows.media.videoframe), [IVectorView](/uwp/api/windows.foundation.collections.ivectorview_t_), [IMapView](/uwp/api/windows.foundation.collections.imapview_k_v_), etc.) from your application and bind them to your binding object from step 4 (by accessing the corresponding ISkillFeature indexed via its name and calling ISkillFeature.SetFeatureValueAsync(Object)).

6) Run your skill over your binding object (by calling ISkill.EvaluateAsync).

7) Retrieve your output primitives from your binding object instantiated in step 4 (by accessing the corresponding ISkillFeature indexed via its name and calling the getter ISkillFeature.FeatureValue).

8) Rinse and repeat from step 5 using the same binding object and new primitives.

To see this in action, refer to the tutorial on ingesting a [Windows Vision Skill from an app](tutorial1.md).

[!INCLUDE [help](../includes/get-help-vision.md)]
