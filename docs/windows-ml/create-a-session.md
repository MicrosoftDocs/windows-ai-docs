---
title: Create a session
description: Learn how to create a session that you can use to bind a model to a device, which can then run and evaluate the model.
ms.date: 4/1/2019
ms.topic: article
keywords: windows 10, windows ai, windows ml, winml, windows machine learning
ms.localizationpriority: medium
---

# Create a session

Once you load a [LearningModel](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodel), you create a [LearningModelSession](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsession), which binds the model to a device that runs and evaluates the model.

## Choose a device

You can select a device when you create a session. You choose a device of type [LearningModelDeviceKind](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodeldevicekind):

* **Default**
	* Let the system decide which device to use. Currently, the default device is the CPU.
* **CPU**
	* Use the CPU, even if other devices are available.
* **DirectX**
	* Use a DirectX hardware acceleration device, specifically the first adapter enumerated by [IDXGIFactory1::EnumAdapters1](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgifactory1-enumadapters1).
* **DirectXHighPerformance**
	* Same as **DirectX**, but will use [DXGI_GPU_PREFERENCE_HIGH_PERFORMANCE](https://docs.microsoft.com/windows/desktop/api/dxgi1_6/ne-dxgi1_6-dxgi_gpu_preference) when enumerating adapters.
* **DirectXMinPower**
	* Same as **DirectX**, but will use [DXGI_GPU_PREFERENCE_MINIMUM_POWER](https://docs.microsoft.com/windows/desktop/api/dxgi1_6/ne-dxgi1_6-dxgi_gpu_preference) when enumerating adapters.

If you don't specify a device, the system uses **Default**. We recommend using **Default** to get the flexibility of allowing the system to choose for you in the future.

The following video goes into more detail about each of the device kinds.

<br/>

> [!VIDEO https://www.youtube.com/embed/NM5CYUMMp-w]

## Example

The following example shows how to create a session from a model and a device:

```cs
private void CreateSession(LearningModel model, LearningModelDeviceKind kind)
{
    // Create the evaluation session with the model and device
    LearningModelSession session =
        new LearningModelSession(model, new LearningModelDevice(kind));
}
```

## See also

* Previous: [Load a model](load-a-model.md)
* Next: [Bind a model](bind-a-model.md)

[!INCLUDE [help](../includes/get-help.md)]
