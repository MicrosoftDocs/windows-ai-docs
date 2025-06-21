---
title: Responsible AI and safety for App Actions on Windows
description: Learn about the Responsible AI and safety considerations when implementing App Actions on Windows.
ms.topic: article
ms.date: 04/30/2025

#customer intent: As a developer, I want implement a Windows App Action so that I can provide a unit of functionality that can be accessed from the App Action on Windows ecosystem.

---


# Responsible AI and safety for App Actions on Windows

Learn about the Responsible AI and safety considerations when implementing App Actions on Windows.

## Responsible AI

When building AI backed actions, it is your responsibility as the action author to perform content moderation and abuse monitoring when it comes to entities returned to the user. For more information about Microsoft Responsible AI policies for more information, see [Microsoft Responsible AI: Principles and approach](https://www.microsoft.com/ai/principles-and-approach).
 
## Content Age Rating

When designing app actions, consider whether the content and functionality is appropriate for users of all ages. In the action definition JSON file, action providers can use the *contentAgeRating* property to specify the appropriate ages for each action. The value of this property is a member name from the [UserAgeConsentGroup](/uwp/api/windows.system.userageconsentgroup) enumeration. The supported values are "Child", "Minor", "Adult". If no value is specified, the default behavior allows access to all ages. For more information on the action definition JSON file format, see [Action definition JSON schema for App Actions on Windows](/windows/ai/app-actions/actions-json).
