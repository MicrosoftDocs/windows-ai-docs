---
title: Click to Do overview
description: Learn how to use the AI-assisted Click to Do feature in Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 02/06/2025
ms.topic: overview
no-loc: [Click to Do, Windows Copilot Runtime, Phi Silica]
---

**Click to Do** is an AI-supported feature that utilizes the local Phi Silica model in Copilot+ PCs to connect actions to the content (text or images) found by Recall. For example, a video that you watched featured a person wearing a shirt that you like. You can find that video and where the shirt shows up using Recall. Click to Do will then offer the option to search using Bing for where you might buy that shirt online. Developers can launch Click to Do from their app using a URI scheme.

Learn more about [Click to Do](https://support.microsoft.com/windows/click-to-do-in-recall-do-more-with-what-s-on-your-screen-967304a8-32d1-4812-a904-fad59b5e6abf).

## Launch Click to Do

Your app can launch the Click to Do feature on a Copilot+ PC by utilizing the following URI scheme: `ms-recall://default/?screenray`.

Currently, for Click to Do to function, the Recall feature must be enabled on the Copilot+ PC. See the "User choice from the start" section of [Privacy and control over your Recall experience](https://support.microsoft.com/windows/privacy-and-control-over-your-recall-experience-d404f672-7647-41e5-886c-a3c59680af15) for guidance on enabling or disabling Recall in the Privacy & security section of Windows Settings.

The `ms-recall://default/?screenray` URI enables your app to programmatically launch Click to Do, placing an interactive overlay on top of the PC screen. This overlay suggests quick actions to appear for images or text. The analysis of the screen is always performed locally on the device. Content is only shared if the user chooses to complete an action. Content is not saved, nor is it automatically passed back to the app used to open the overlay. This URI does not accept any additional parameters.

The following code samples open Click to Do from the user's app:

```cppwinrt
winrt::Windows::Foundation::Uri clickToDoUri(L"ms-recall://default/?screenray"); 

winrt::Windows::System::Launcher::LaunchUriAsync(clickToDoUri); 
```

```csharp
var clickToDoUri = new Windows.Foundation.Uri(L"ms-recall://default/?screenray"); 

Windows.System.Launcher.LaunchUriAsync(clickToDoUri) 
```

## Related content

- [Recall](./recall.md)
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)