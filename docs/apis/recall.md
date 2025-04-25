---
title: Recall overview
description: Learn how to use the AI-assisted Recall feature with the User Activity API in Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 02/06/2025
ms.topic: overview
no-loc: [Recall, Click to Do, Windows Copilot Runtime, Phi Silica]
---

# Recall overview

**Recall** utilizes [Windows Copilot Runtime](../overview.md) to help you find anything you've seen on your PC. Search using any clues you remember or use the timeline to scroll through your past activity, including apps, documents, and websites. Once you've found what you're looking for, you can quickly jump back to the content seen in the snapshot by selecting the relaunch button below the screenshot. The **UserActivity API** is what allows apps to provide deep links, so you can pick up where you left off.

[Learn more about Recall](https://support.microsoft.com/windows/retrace-your-steps-with-recall-aa03f8a0-a78b-4b3e-b0a1-2eb8ac48701c), including:

- System Requirements
- How to use Recall
- How to search with Recall
- How content interaction (with ["Screenray"](https://support.microsoft.com/windows/retrace-your-steps-with-recall-aa03f8a0-a78b-4b3e-b0a1-2eb8ac48701c#:~:text=Recall%20opens%20the%20snapshot%20and,cursor%20is%20blue%20and%20white.)) works
- How to pause or resume snapshots
- How to filter certain websites or apps from being saved by Recall
- How to manage Recall snapshots and disk space
- Keyboard shortcuts

Learn more about [Privacy and control over your Recall experience](https://support.microsoft.com/windows/privacy-and-control-over-your-recall-experience-d404f672-7647-41e5-886c-a3c59680af15), including:

- Controls on how to manage your Recall and snapshots preferences
- How to filter apps and websites from your snapshots
- How snapshot storage works (content stays local)
- Built-in security included with the secured-core PC, Microsoft Pluton security processor, and Windows Hello Enhanced Sign-in Security (ESS)

For IT Administrators, learn how to [Manage Recall](/windows/client-management/manage-recall) using Windows Client Management, including:

- System requirements
- Supported browsers
- How to configure policies for Recall
- Limitations
- User controlled settings for Recall
- Storage allocation
- Policy setting for control over whether Windows saves snapshots of the screen and analyzes user activity on the device: [DisableAIDataAnalysis](/windows/client-management/mdm/policy-csp-windowsai#disableaidataanalysis)

Recall updates:

- [Previewing Recall with Click to Do on Copilot+ PCs with Windows Insiders in the Dev Channel (November 22, 2024)](https://blogs.windows.com/windows-insider/2024/11/22/previewing-recall-with-click-to-do-on-copilot-pcs-with-windows-insiders-in-the-dev-channel/)
- [Update on Recall security and privacy architecture (September 27, 2024)](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/)
- [Update on the Recall preview feature for Copilot+ PCs (June 7, 2024)](https://blogs.windows.com/windowsexperience/2024/06/07/update-on-the-recall-preview-feature-for-copilot-pcs/).

## Prerequisites

- A [Copilot+ PC](/windows/ai/npu-devices/) from Qualcomm, Intel, or AMD.
  - Arm64EC (Emulation Compatible) is not currently supported.
- The April 2025 Windows non-security preview update or later must be installed on your device.
  - Consumers with Copilot+ PCs can be among the first to experience these new features by going to: Settings > Windows Update and turning on “Get the latest updates as soon as they’re available.” Then select “Check for Updates” to download and install the April non-security preview release. In some cases, features may be provided via a separate update.

## Use Recall in your Windows app

For those who opt-in by [enabling "Recall & snapshots" in Settings > Privacy & security](https://support.microsoft.com/windows/privacy-and-control-over-your-recall-experience-d404f672-7647-41e5-886c-a3c59680af15#:~:text=You%20can%20turn%20on%20or,and%20selecting%20the%20pause%20option), Windows will regularly save snapshots of the customer's screen and stores them locally. Using screen segmentation and image recognition, Windows provides the power to gain insight into what is visible on the screen. Users of your Windows applications will now be able to semantically search these saved snapshots and find content related to your app. And to support relaunching back into the content seen in your app, you can provide a [`UserActivity`](/uwp/api/windows.applicationmodel.useractivities) so that Recall knows how to open the content that was visible in your app.

![Screenshot of the Recall interface showing a Redbarn Sale Analysis app sample.](../images/recall-redbarn.png)

### Enable relaunching via user activities

While screenshots are automatically captured, users won't be able to relaunch your content unless you provide a UserActivity at the time of capture. This enables Recall to launch the user back into what was being seen at that time.

A **UserActivity** refers to something specific the user was working on within your app. For example, when a user is writing a document, a `UserActivity` could refer to the specific place in the document where the user left off writing. When listening to a music app, the `UserActivity` could be the playlist that the user last listened to. When drawing on a canvas, the `UserActivity` could be where the user last made a mark. In summary, a `UserActivity` represents a destination within your Windows app that a user can return to so that they can resume what they were doing.

There are two ways you can provide activities...

* **Push model (recommended)**: Your app pushes new activities whenever content changes. For example, in an email app, when the user opens an email, you send a new user activity for that email.
* **Pull model**: Your app registers to a *Requested* event handler, and Windows will periodically request user activities from your app. You respond with an activity that represents what content is currently open in your app.

#### User activities via pushing

Whenever the main content in your app changes (like the user opening a different email, opening a different webpage, etc), your app must log a new UserActivitySession so that the system knows what content is currently open.

```csharp

UserActivitySession _previousSession;

private async Task OnContentChangedAsync()
{
    // Dispose of any previous session (which automatically logs the end of the interaction with that content)
    _previousSession?.Dispose();

    // Generate an identifier that uniquely maps to your new content.
    string id = "doc135.txt";

    // Get or create a new user activity that represents your new content
    var activity = await UserActivityChannel.GetDefault().GetOrCreateUserActivityAsync(id);

    // Populate the required properties
    activity.DisplayText = "doc135.txt";
    activity.ActivationUri = new Uri("my-app://docs/doc135.txt");

    // Save the activity
    await activity.SaveAsync();

    // And start a new session tracking the engagement with this new activity
    _previousSession = activity.CreateSession();
}

```

#### User activities via pulling

Alternatively, your app can register to an event handler, and on-demand provide an activity representing the current content whenever Windows requests a current activitiy.

```csharp
public void OnLaunched()
{
    UserActivityRequestManager.GetForCurrentView().UserActivityRequested += UserActivityRequested;
}

private async void UserActivityRequested(
    Windows.ApplicationModel.UserActivities.UserActivityRequestManager sender,
    Windows.ApplicationModel.UserActivities.UserActivityRequestedEventArgs args)
{
    // Start a deferral so you can use async code
    var deferral = args.GetDeferral();

    try
    {
        // Generate an identifier that uniquely maps to your current content.
        string id = "doc135.txt";

        // Get or create a user activity that represents your current content
        var activity = await UserActivityChannel.GetDefault().GetOrCreateUserActivityAsync(id);

        // Populate the required properties
        activity.DisplayText = "doc135.txt";
        activity.ActivationUri = new Uri("my-app://docs/doc135.txt");

        // And return the activity to the event handler
        args.Request.SetUserActivity(activity);
    }

    finally
    {
        // And complete the deferral
        deferral.Complete();
    }
}
```

### Opting out of capture

If your app needs to temporarily opt-out of capture, like when the user is in an InPrivate browsing mode within your app, you can TODO

```
calling SetInputScope and setting IS_PRIVATE on their HWND
```

## Related content

- [Click to Do](./click-to-do.md)
- [Developing Responsible Generative AI Applications and Features on Windows](../rai.md)