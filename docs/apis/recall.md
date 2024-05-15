---
title: Recall API Overview
description: Learn how to use the AI-assisted Recall feature with the User Activity API in Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 05/21/2024
ms.topic: overview
---

# Recall API Overview

Recall allows you to find anything you’ve seen on your PC. Search using any clues you remember or use timeline to scroll through your past activity, including apps, documents, and websites. Once you’ve found what you are looking for, you can quickly jump back to the original content seen in the snapshot by selecting the relaunch action below the
screenshot. The UserActivity API is what allows apps to provide deep links, you can pick up where you left off.

The Recall system component regularly saves snapshots of the customer’s screen and stores them locally. Through both screen and text segmentation, Windows provides the power to gain insight into what is visible on the screen. Saving these snapshots gives all your users, as application owners, the ability to semantically search and find related
content from your application. Each snapshot has User Activities tied to them to enable relaunching of the content.

## How does Recall relate to the Windows UserActivity API?

Think of a **UserActivity** as something specific the user was working on within your app. For example, if you are using a RSS reader, a **UserActivity** could be the feed that you are reading. If you are playing a game, the **UserActivity** could be the level that you are playing. If you are listening to a music app, the **UserActivity** could
be the playlist that you are listening to. If you are working on a document, the **UserActivity** could be where you left off working on it, and so on. In short,  **UserActivity** represents a destination within your app so that enables the user to resume what they were doing.

When you engage with a **UserActivity** by calling `UserActivity.CreateSession`, the system creates a history record indicating the start and end time for that **UserActivity**. As you re-engage with that **UserActivity** over time, multiple history records are recorded for it.

## Prerequisites

The following prerequisites must be met in order to use the Recall feature:

- Recall will currently be available only on the new Copilot AI PC.  
- Most recent version of Windows 11 must be installed.
- Windows App SDK version 17134

## Use the UserActivity API to add User Activities to your Windows app

A UserActivity is the unit of user engagement in Windows. It has three parts: a URI used to activate the app the activity belongs to, visuals,
and metadata that describes the activity.

The [ActivationUri](/uwp/api/windows.applicationmodel.useractivities.useractivity.activationuri#Windows_ApplicationModel_UserActivities_UserActivity_ActivationUri) is
used to resume the application with a specific context. Typically, this link takes the form of protocol handler for a scheme (for example,
“my-app://page2?action=edit”) or of an AppUriHandler (for example, http://contoso.com/page2?action=edit).

[VisualElements](/uwp/api/windows.applicationmodel.useractivities.useractivity.visualelements) exposes a class that allows the user to visually identify an activity with a
title, description, or Adaptive Card elements.

Finally, [Content](/uwp/api/windows.applicationmodel.useractivities.useractivityvisualelements.content#Windows_ApplicationModel_UserActivities_UserActivityVisualElements_Content) is where you can store metadata for the activity that can be used to group and retrieve activities under a specific context. Often, this takes the form of [https://schema.org](https://schema.org/) data.

To add a **UserActivity** to your app:

- Generate **UserActivity** objects when your user’s context changes within the app (such as page navigation, new game level, etc.)

- Populate **UserActivity** objects with the minimum set of required fields: ActivityId, ActivationUri, and UserActivity.VisualElements.DisplayText.

Add a custom scheme handler to your app so it can be re-activated by a **UserActivity**.

A **UserActivity** can be integrated into an app with just a few lines of code. For example, imagine this code in MainPage.xaml.cs, inside the MainPage class (note: assumes using
Windows.ApplicationModel.UserActivities;):

```csharp
UserActivitySession \_currentActivity; 

private async Task GenerateActivityAsync() 

{ 

    // Get the default UserActivityChannel and query it for our
UserActivity. If the activity doesn't exist, one is created. 

    UserActivityChannel channel = UserActivityChannel.GetDefault(); 

    UserActivity userActivity = await
channel.GetOrCreateUserActivityAsync("MainPage"); 

  

    // Populate required properties 

    userActivity.VisualElements.DisplayText = "Hello Activities"; 

    userActivity.ActivationUri = new Uri("my-app://page2?action=edit"); 

      

    //Save 

    await userActivity.SaveAsync(); //save the new metadata 

  

    // Dispose of any current UserActivitySession, and create a new
one. 

    \_currentActivity?.Dispose(); 

    \_currentActivity = userActivity.CreateSession(); 

} 
```

The first line in the `GenerateActivityAsync()` method above gets a user’s UserActivityChannel. This is the feed that this app’s activities will be published to. The next line queries the channel of an activity called MainPage.

Your app should name activities in such a way that same ID is generated each time the user is in a particular location in the app. For example, if your app is page-based, use an identifier for the page; if its document based, use the name of the doc (or a hash of the name).

If there is an existing activity in the feed with the same ID, that activity will be returned from the channel with `UserActivity.State` set to [Published](/uwp/api/windows.applicationmodel.useractivities.useractivitystate). If there is no activity with that name, and new activity is returned with UserActivity.State set to **New**.

Activities are scoped to your app. You do not need to worry about your activity ID colliding with IDs in other apps.

After getting or creating the **UserActivity**, specify the other two required fields: UserActivity.VisualElements.DisplayTextand UserActivity.ActivationUri.

Next, save the **UserActivity** metadata by calling SaveAsync, and finally CreateSession, which returns a UserActivitySession. The **UserActivitySession** is the object that we can use to manage when the user is actually engaged with the **UserActivity**. For example, we should call Dispose() on the **UserActivitySession** when the user leaves the page. In the example above, we also call Dispose() on \_currentActivity before calling CreateSession(). This is because we made \_currentActivity a member field of our page, and we want to stop any existing activity before we start the new one (note: the ? is the [null-conditional operator](/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-) which tests for null before performing the member access).

Since, in this case, the ActivationUri is a custom scheme, we also need to register the protocol in the application manifest. This is done in the Package.appmanifest XML file, or by using the designer.

To make the change with the designer, double-click the Package.appmanifest file in your project to launch the designer, select the **Declarations** tab and add a **Protocol** definition. The only property that needs to be filled out, for now, is **Name**. It should match the URI we specified above, my-app.

Now we need to write some code to tell the app what to do when it’s been activated by a protocol. We’ll override the OnActivated method in App.xaml.cs to pass the URI on to the main page, like so:

```csharp
protected override void OnActivated(IActivatedEventArgs e) 

{ 

    if (e.Kind == ActivationKind.Protocol) 

    { 

        var uriArgs = e as ProtocolActivatedEventArgs; 

        if (uriArgs != null) 

        { 

            if (uriArgs.Uri.Host == "page2") 

            { 

                // Navigate to the 2nd page of the  app 

            } 

        } 

    } 

    Window.Current.Activate(); 

} 
```

What this code does is detect whether the app was activated via a protocol. If it was, then it looks to see what the app should do to resume the task it is being activated for. Being a simple app, the only activity this app resumes is putting you on the secondary page when the app comes up.

Learn more: [Windows.ApplicationModel.UserActivities Namespace](/uwp/api/windows.applicationmodel.useractivities).

## General guidelines

**Record a single activity for a group of related user actions.** This is especially relevant for music playlists or TV Shows: a single Activity can be updated at regular intervals to reflect the user's progress. In this case, you will have a single User Activity with multiple History Items representing periods of engagement across multiple days or weeks. The same applies to document-based activities on which the user makes gradual progress within your app.

**Do not create Activities for actions that users will not need to resume.** If your application is used to complete simple, one-time operations that do not persist status, you probably do not need to create a User Activity.

**Do not create Activities for actions completed by other users.** If an external account sends the user a message or @-mentions them within your app, you should not create an Activity for this. This type of action is better served by Action Center Notifications.

Collaboration scenarios are an exception: If multiple users are working on the same activity together (such as a Word document), there will be cases in which another user has made changes after your user. In this case, you may want to update the existing Activity to reflect changes that were made to the document. This would involve updating the existing User Activity content data without creating a new History Item.

## Guidelines for specific types of apps

While every app is different, most apps will fall into one of the following interaction patterns.

**Document-based apps** — Create one Activity per document, with one or more History Items reflecting periods of use. It is important to update your Activity as changes are made to the document.

**Games** — Create one Activity for each game save or world. If your game supports only a single sequence of levels, you can re-publish the same Activity over time, although you may wish to update the content data to show the latest progress or achievements.

**Utility apps** — If there is nothing within your app that users would need to leave and resume, you do not need to use User Activities. A good example is a simple app like Calculator.

**Line-of-business apps** — Many apps exist for managing simple tasks or workflows. Create one activity for each separate workflow accessed through your app (for example, expense reports would each be a separate Activity, so that the user could then click an Activity to see if a particular report was approved).

**Media playback apps** — Create one Activity per logical grouping of content (such as a playlist, program, or standalone content). The underlying question for app developers is whether a each piece of content (TV episode, song) counts as standalone content or part of a collection. As a general rule, if the user opts to play a collection or sequential content, the collection as a whole is the activity. If they opt to play a single piece of content, then that one piece of content is the activity. See more specific guidelines below.

**Music: Album/Artist/Genre** — If the user selects an Album, Artist, or Genre and hits **play**, that collection is the activity; do not write a separate Activity for each song. For short collections like a single album or collections being played back in a random order, you may not need to update the Activity to reflect the user's current position. For long sequential playback such as an album or playlist, recording your position within the album might make sense.

**Music: smart playlists** — Applications which play music in a random order should record a single Activity for that playlist. If the user plays the playlist a second time, you would create additional history records for the same Activity. Recording the user's current position in the playlist is not necessary because the ordering is random.

**TV series** — If your app is configured to play the next episode after the current one is complete, you should write a single Activity for the TV series. As you play the various episodes across multiple viewing sessions, you'll update your Activity to reflect the current position in the series, and multiple history records will be created.

**Movie** — A movie is a single piece of content and should have its own history record. If the user stops watching the movie part-way through, it is desirable to record their position. When they wish to resume it in the future, the Activity could resume the movie where they left off, or even ask the user if they wish to resume or start at the beginning.

## User Activity design

User Activities consist of three components: an activation URI, visual data, and content metadata.

The activation URI is a URI that can be passed to an application or experience in order to resume the application with a specific context.
Typically, these links take the form of protocol handler for a scheme (for example, "my-app://page2?action=edit"). It is up to the developer to determine how URI parameters will be handled by their app.

See [Handle URI activation](/windows/uwp/launch-resume/handle-uri-activation) for more information.

The visual data, consisting of a set of required and optional properties (for example: title, description), further helps users to identify an Activity in Recall.

The content metadata is JSON data that can be used to group and retrieve activities under a specific context. Typically, this takes the form
of [http://schema.org](http://schema.org/) data.

## Additional Resources

- [**UserActivity** API](/uwp/api/windows.applicationmodel.useractivities)

- [Project Rome](https://github.com/Microsoft/project-rome)

- [UserActivities namespace](/uwp/api/windows.applicationmodel.useractivities) 
