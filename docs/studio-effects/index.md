---
title: Windows Studio Overview
description: Windows Studio applies AI effects that utilize the device camera (currently supported) or microphone (coming soon), including Background Blur, Background Segmentation, Eye Contact and Auto Framing, leveraging NPU to optimize performance and using standardized control interaces.
author: mattwojo 
ms.author: mattwoj 
manager: jken
ms.topic: article
ms.date: 11/22/2023
---

# Windows Studio Overview - Apply AI Effects and Camera Settings (Preview)

Windows Studio applies AI effects that utilize the device camera (currently supported) or microphone (coming soon), including Background Blur, Background Segmentation, Eye Contact and Auto Framing.

Windows Studio standardizes control interfaces for camera and microphone (Kernel Streaming properties and APIs) that any application can use to discover if effects are supported, turning effects on or off as-needed, and accessing any available metadata. Effects are applied at the camera (or microphone) level so that once an effect is turned on in the Camera Settings, it is on by default for any app using the camera, even if the app doesn’t know about the effect.

Windows Studio leverages AI models built by Microsoft and compiled/optimized for device's with Neural Processing Units (NPUs) to deliver high-fidelity, battery-friendly AI effects that reduce the burdon on the device CPU and GPU and provide a trusted Microsoft AI experience that scales across the entire Windows ecosystem for any compatible devices.

## Prerequisites

- Windows 11, version 22H2 or newer.
- TBD

## Windows Studio Architecture

When a camera is opted into Windows Studio, the Windows Studio package gets chained on to the end of the camera. This happens transparently so that the “real” camera is replaced with a “composite” camera consisting of the features of the camera plus the Windows Studio AI effects. The end customer still sees only the “real” camera, but the Windows Studio effects are now available on behalf of that camera.

![Diagram showing the "composite" camera surrounding the "real" camera and OEM driver with properties listed including brightness, contrast, other Microsoft properties, and customer OEM properties. The "real" camera connects to the Windows Studio effects including AI blur and AI eye contact, resulting in a list of the combined properties from the "real" camera and Windows Studio.](../images/windows-studio-architecture-diagram.png)

The "Real" camera includes [Kernal Streaming (KS)](/windows-hardware/drivers/stream/ks-properties) properties, such as Brightness, Contrast, and other Microsoft-implemented properties, as well as any customer properties implemented by the device manufacturer (OEM) driver.

Since Windows Studio is always the last item in the chain, applications can be assured that if Windows Studio is enabled for a camera, that the Background Blur, Background Segmentation, Eye Contact, and Automatic Framing KS properties implemented by the camera are provided by Windows Studio.

When the camera **is not opted in** to using Windows Studio, any apps accessing the camera see only the "Real" camera KS properties (Brightness, Contrast, etc).

When the camera **is opted in** to using Windows Studio, any apps accessing the camera can see the both the "Real" camera KS properties, in addition to the Windows Studio KS properties representing AI effects, such as Background Blur, Eye Contact, etc.

In the event of a second implementation of the same KS Property lower in the chain (for example, a [DMFT from the OEM](/windows-hardware/drivers/stream/dmft-design) also implements Background Blur effect), that implementation will remain OFF since the default value for the Blur KS Property is OFF. When Blur is turned ON for the camera, Windows Studio handles that request internally and does not forward it up the chain to other components (DMFTs, AVStream driver, etc.).

This approach allows device manufacturers (OEMs, such as Dell or Lenovo, and IHVs, such as Intel, AMD, or NVIDIA) to implement their own camera processing features within their DMFTs or directly in the camera before Windows Studio adds the standard Windows AI experiences on top of it.

## Camera Settings app

The Camera Settings app is a new feature in Windows 11 that allows customers to view all of the cameras on their system, and then on a per-camera, per-user, per-machine basis, selecting preferred “default” values from a curated set of controls.

The Camera Settings app also supports extensibility via companion apps provided by camera manufacturers. These companion apps allow device manufacturers to offer their own custom user interface to adjust camera settings, and/or to provide controls for additional custom camera effects (for example, an on/off toggle for a “Funny Hat” effect provided by the camera manufacturer).

**TO-DO**: Could use a screenshot of the Camera Settings app here.

### Default Camera Settings

The Camera Settings app can adjust basic controls, such as Brightness and Contrast, but also Windows Studio effects like Background Blur and Eye Contact.

#### Start in a known state

Whenever any application uses Windows APIs to start the camera stream, Windows will set the current value of the Kernel Streaming (KS) property to match the default value specified in the Camera Settings before handing control over to the application. By matching the default value specified in Camera Settings, the camera will always start in a known state.

> [!NOTE]
> If the application has already written a value to a KS Property that also has a default value set from the Settings page before starting the stream, Windows skips applying the user’s default value when starting the stream. For example, if the user’s default brightness is set to 60, but the app sets the current value of brightness to 65 before starting the stream, the camera will start with brightness at 65 instead of 60.

#### Apply additional settings

After the camera stream is set to a known state, an application is welcome to query and apply further configuration, writing any new KS Property it wishes to. If a customer uses an app that is not aware of specific camera controls (for example, Brightness or Background Blur), the settings for those controls that the user specified in the Camera Settings will still apply to the app. But if a customer uses an app that is aware of those controls, the app is able to change the current value of those controls while using the camera.

Applications are not allowed to change the default value of controls, to ensure that one app does not change the behavior of other apps that use the camera. Defaults can only be changed from the Camera Settings app.
In Windows 11, version 22H2, customers who have a device supporting Windows 
Studio can turn the effects on/off directly from the Camera Settings Page, alongside 
other common settings for their cameras

## Supported camera settings and AI effects

List of supported basic camera settings and AI camera effects.

## App integration guidance

Guidance for Windows apps to integrate with camera settings and Windows Studio AI effects.

## Troubleshooting

**Handling overlapping camera effects**

In progress.

**Anything else?**
