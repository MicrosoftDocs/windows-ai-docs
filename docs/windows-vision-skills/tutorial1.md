---
author: eliotcowley
title: Create a vision skill UWP application
description: Integrate a vision skill into a UWP app by following this tutorial.
ms.author: elcowle
ms.date: 4/25/2019
ms.topic: article
keywords: windows 10, windows ai, windows vision skills, uwp
ms.localizationpriority: medium
---

# Tutorial: Create a Windows Vision Skill UWP application

> [!NOTE]
> Some information relates to pre-released product, which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

In the previous [tutorial](tutorial.md), we learned how to create and package a Windows Vision Skill. Now, let's learn how to integrate that into a Universal Windows Platform (UWP) application. You can download the complete sample, available on [GitHub](https://github.com/Microsoft/WindowsVisionSkillsPreview/tree/master/samples/SentimentAnalyzerCustomSkill) to see what it looks like when you finish.

Also with relevance to this tutorial, you can find here more information about loading *[VideoFrames](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame)* from the following sources:
- an [existing *SoftwareBitmap*](https://docs.microsoft.com/uwp/api/windows.media.videoframe.createwithsoftwarebitmap#Windows_Media_VideoFrame_CreateWithSoftwareBitmap_Windows_Graphics_Imaging_SoftwareBitmap_)
- an [image file](https://docs.microsoft.com/windows/uwp/audio-video-camera/imaging#create-a-softwarebitmap-from-an-image-file-with-bitmapdecoder)
- camera via [FrameReader](https://docs.microsoft.com/windows/uwp/audio-video-camera/process-media-frames-with-mediaframereader)
- camera via Microsoft.Toolkit: [CameraPreview](https://docs.microsoft.com/windows/communitytoolkit/controls/camerapreview), [CameraHelper](https://docs.microsoft.com/windows/communitytoolkit/helpers/camerahelper)

---
## Prerequisites

- Completing previous tutorial on [Creating your own Windows Vision Skill](tutorial.md)
---

## API flow
We will revisit the API flow described in the [Important API concepts](important-api-concepts.md#APIFlow) page but now with a concrete set of classes in C#. The full sample code is available on [GitHub](https://github.com/Microsoft/WindowsVisionSkillsPreview/blob/master/samples/SentimentAnalyzerCustomSkill/cs/FaceSentimentAnalysisTestApp/MainPage.xaml.cs). 

We'll cover the lines of code relevant to the Windows Vision Skill API: 

+ Instantiate the [ISkillDescriptor][ISkillDescriptor] derivative

    ```csharp
    ...
    
    // member variable to hold the skill descriptor
    private FaceSentimentAnalyzerDescriptor m_skillDescriptor = null;
    
    ...
    
    // Instatiate skill descriptor to display details about the skill and populate UI
    m_skillDescriptor = new FaceSentimentAnalyzerDescriptor();

    ...
    ```

+ Query the available execution devices using the [ISKillDescriptor][ISKillDescriptor] instance
    ```csharp
    ...
    
    // member variable to hold the available execution devices
    private IReadOnlyList<ISkillExecutionDevice> m_availableExecutionDevices = null;
    
    ...
    
    m_availableExecutionDevices = await m_skillDescriptor.GetSupportedExecutionDevicesAsync();

    ...
    ```

+ Instantiate the skill using the [ISKillDescriptor][ISKillDescriptor] instance and the desired [ISkillExecutionDevice][ISkillExecutionDevice]
    ```csharp
    ...
    
    // member variable to hold the skill
    private FaceSentimentAnalyzerSkill m_skill = null;
    
    ...
    
    // Initialize skill with the selected supported device
    m_skill = await m_skillDescriptor.CreateSkillAsync(m_availableExecutionDevices[UISkillExecutionDevices.SelectedIndex]) as FaceSentimentAnalyzerSkill;

    ...
    ```

+ Instantiate a skill binding object using the [ISKill][ISKill] instance
    ```csharp
    ...
    
    // member variable to hold the skill binding
    private FaceSentimentAnalyzerBinding m_binding = null;
    
    ...
    
   // Instantiate a binding object that will hold the skill's input and output resource
   m_binding = await m_skill.CreateSkillBindingAsync() as FaceSentimentAnalyzerBinding;

    ...
    ```

+ Retrieve your input primitive (*VideoFrame*) and bind it to your binding object by accessing the corresponding [ISkillFeature][ISkillFeature] indexed via its name. Note that this skill declares a convenience set method *SetInputImage*
    ```csharp
    ...

    // Retrieve a VideoFrame from an image file
    VideoFrame frame = await LoadVideoFrameFromFilePickedAsync();

    ...

    // Update input image feature
    await m_binding.SetInputImageAsync(frame);

    ...
    ```
    this convenience method could be bypassed and has the same effect as setting the value directly like so:

    ```csharp
    // Update input image feature
    await m_binding["InputImage"].SetFeatureValueAsync(frame);
    ```

+ Run your skill over your binding object
    ```csharp
    // Evaluate the binding
    await m_skill.EvaluateAsync(m_binding);
    ```

+ Retrieve your output primitives from your binding object. Note that this skill declares a convenience getter property named *IsFaceFound*.
    ```csharp
    // Retrieve results
    if (m_binding.IsFaceFound)
    {
        IReadOnlyList<float> scores = (m_binding["FaceSentimentScores"].FeatureValue as SkillFeatureTensorFloatValue).GetAsVectorView();
    }
    ```

    This convenience method, as detailed in the previous tutorial and below, has the same effect as obtaining the output feature directly from the binding and validating its values are not zeros.

    ```csharp
    public bool FaceSentimentAnalyzerBinding.IsFaceFound
    {
        get
        {
            ISkillFeature feature = null;
            if (m_bindingHelper.TryGetValue("FaceRectangle", out feature))
            {
                var faceRect = (feature.FeatureValue as SkillFeatureTensorFloatValue).GetAsVectorView();
                return !(faceRect[0] == 0.0f &&
                    faceRect[1] == 0.0f &&
                    faceRect[2] == 0.0f &&
                    faceRect[3] == 0.0f);
            }
            else
            {
                return false;
            }
        }
    ```



[!INCLUDE [help](../includes/get-help-vision.md)]

[SkillInterfacePreview]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview

[ISkillDescriptor]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskilldescriptor

[ISkill]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskill

[ISkillBinding]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskillbinding

[ISkillExecutionDevice]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskillexecutiondevice

[ISkillFeature]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskillfeature

[ISkillFeatureValue]: https://docs.microsoft.com/dotnet/api/microsoft.ai.skills.skillinterfacepreview.iskillfeaturevalue
