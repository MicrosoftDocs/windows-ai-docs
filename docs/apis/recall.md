---
title: Recall Overview
description: Learn how to use the AI-assisted Recall feature with the User Activity API in Windows.
ms.author: mattwoj
author: mattwojo
ms.date: 05/21/2024
ms.topic: overview
---

# Recall Overview

Recall utilizes [Windows AI Runtime](../overview.md) to help you find anything you’ve seen on your PC. Search using any clues you remember or use the timeline to scroll through your past activity, including apps, documents, and websites. Once you’ve found what you're looking for, you can quickly jump back to the content seen in the snapshot by selecting the relaunch button below the screenshot. The **UserActivity API** is what allows apps to provide deep links, so you can pick up where you left off.

## Prerequisites

- Currently available only on the new Copilot+ PC.
- User Activity is supported in Windows SDK version 10.0.17134.0 (Windows 10, version 1803, Build 17134) or later.

## Use Recall in your Windows app

The Recall system component regularly saves snapshots of the customer’s screen and stores them locally. Using screen segmentation and image recognition, Windows provides the power to gain insight into what is visible on the screen. As a Windows application developer, you will now be able to offer your app users the ability to semantically search these saved snapshots and find content related to your app. Each snapshot has a [`UserActivity`](/uwp/api/windows.applicationmodel.useractivities) associated that enables the user to relaunch the content.

![Screenshot of the Recall interface showing a Redbarn Sale Analysis app sample.](../images/recall-redbarn.png)

### User Activities

A **UserActivity** refers to something specific the user was working on within your app. For example, when a user is writing a document, a `UserActivity` could refer to the specific place in the document where the user left off writing. When listening to a music app, the `UserActivity` could be the playlist that the user last listened to. When drawing on a canvas, the `UserActivity` could be where the user last made a mark. In summary, a `UserActivity` represents a destination within your Windows app that a user can return to so that they can resume what they were doing.

To engage with a `UserActivity` your Windows app would call: [UserActivity.CreateSession](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession). The Windows operating system responds by creating a history record indicating the start and end time for that `UserActivity`. Re-engaging with that same `UserActivity` over time will result in multiple history records being stored for it.

More information about how to engage with User Activities using Recall is coming soon.
