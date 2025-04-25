---
title: Enable relaunching your content from Recall
description: Learn how to enable your users to relaunch back to your content from Windows Recall.
ms.author: aleader
author: aleader
ms.date: 04/25/2025
ms.topic: article
no-loc: [Recall, relaunch, user activity, useractivity]
---

# Enable relaunching your content from Recall

Recall automatically captures screenshots of your application, however, users won't be able to relaunch back to your content unless you provide a UserActivity at the time of capture. This enables Recall to launch the user back into what was being seen at that time.

A **UserActivity** refers to something specific the user was working on within your app. For example, when a user is writing a document, a `UserActivity` could refer to the specific place in the document where the user left off writing. When listening to a music app, the `UserActivity` could be the playlist that the user last listened to. When drawing on a canvas, the `UserActivity` could be where the user last made a mark. In summary, a `UserActivity` represents a destination within your Windows app that a user can return to so that they can resume what they were doing.

There are two ways you can provide activities...

* **Push model (recommended)**: Your app pushes new activities whenever content changes. For example, in an email app, when the user opens an email, you send a new user activity for that email.
* **Pull model**: Your app registers to a *Requested* event handler, and Windows will periodically request user activities from your app. You respond with an activity that represents what content is currently open in your app.

## User activities via pushing

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

## User activities via pulling

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