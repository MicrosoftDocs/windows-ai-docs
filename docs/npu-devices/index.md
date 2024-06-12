---
title: Windows Copilot+ PCs Developer Guide
description: Developer guide for Windows Copilot+ PCs.
author: mattwojo 
ms.author: mattwoj 
manager: jken
ms.topic: article
ms.date: 06/18/2024
no-loc: [Windows Copilot]
---

# Windows Copilot+ PCs Developer Guide

Copilot+ PCs are a new class of Windows 11 PCs that are powered by a turbocharged neural processing unit (NPU) — a specialized computer chip for AI-intensive processes like real-time translations and image generation—that can perform more than 40 trillion operations per second (TOPS).

Copilot+ PCs are the fastest, most intelligent Windows PCs ever built. With powerful new silicon capable of an incredible 40+ TOPS (trillion operations per second), all–day battery life and access to the most advanced AI models, Copilot+ PCs will enable you to do things you can’t on any other PC. This article will guide you through how to utilize the NPU for developing these incredible machines.

Learn more at [Introducing Copilot+ PCs - The Official Microsoft Blog](https://blogs.microsoft.com/blog/2024/05/20/introducing-copilot-pcs/).

The following Copilot+ PC Developer Guidance covers:

- Device Prerequisites
- How to access the NPU programmatically
- How to utilize multitasking
- Tools to measure performance of the NPU

## Prerequisites

This guidance is specific to [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs), including but not limited to:

- [Copilot+ PCs](https://www.microsoft.com/windows/copilot-plus-pcs) powered by the Arm-based [Qualcomm Snapdragon X Elite](https://www.qualcomm.com/products/mobile/snapdragon/pcs-and-tablets/snapdragon-x-elite).
- Intel Lunar Lake -- *Coming soon*
- AMD STRIX (Ryzen AI 9) -- *Coming soon*

## What is the Arm-based Snapdragon Elite X+ chip?

The revolutionary new Snapdragon X Elite+ SoC Arm-based chip built by Qualcomm emphasizes AI integration through its industry-leading Neural Processing Unit (NPU). This powerful NPU can perform 45 trillion operations per second, achieving operations 100 times more efficient than other compute chips in this class.

Learn more about using and developing for [Windows on Arm](/windows/arm/overview).

## What unique AI features do Snapdragon X NPU-enabled devices support?

Copilot+ PCs offer unique AI experiences that ship with modern versions of Windows 11. These AI features, designed to run on the device NPU, include:

- [Windows Studio Effects](/windows/ai/studio-effects/): a set of audio and video NPU-accelerated AI effects from Microsoft including Creative Filter, Background Blur, Eye Contact, Auto Framing, Voice Focus. Developers can also add toggles to their app for system level controls.

- [Recall](/windows/ai/apis/recall): the AI-supported UserActivity API that enables users to search for past interactions using natural language and pick up where they left off. Learn more: [Retrace your steps with Recall](https://support.microsoft.com/windows/retrace-your-steps-with-recall-aa03f8a0-a78b-4b3e-b0a1-2eb8ac48701c)

- [Phi Silica](/windows/ai/apis/phi-silica): The Phi Small Language Model (SLM) that enables your app to connect with the on-device model to perform natural language processing tasks (chat, math, code, reasoning) using the Windows App SDK (available beginning in the Experimental 2 release).

- [Text Recognition](/windows/ai/apis/text-recognition): The Optical Character Recognition (OCR) API that enables the extraction of text from images and documents. Imagine tasks like converting a PDF, paper document, or picture of a classroom white board into editable digital text.

- [Cocreator with Paint](https://support.microsoft.com/windows/use-cocreator-in-paint-53857513-e36c-472d-8d4a-adbcd14b2e54) a new feature in Microsoft Pain that transforms images into AI Art.

- [Super Resolution](https://devblogs.microsoft.com/directx/autosr/): an industry-leading AI technology that uses the NPU to make games run faster and look better.

**Not all features may initially be available on all Copilot+ PCs.*
