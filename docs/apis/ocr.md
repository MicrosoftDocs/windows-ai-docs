---
title: Get started using the OCR API
description: TODO Description needed.
ms.author: mattwoj
author: mattwojo
ms.date: 05/21/2024
ms.topic: overview
#customer intent: As a <role>, I want <what> so that <why>.
---

# Get started using the OCR API

Optical Character Recognition (OCR) is a technology that enables the recognition of text in an image and the conversion of different types of documents, such as scanned paper documents, PDF files or images captured by a digital camera, into editable and searchable data.

The OCR API allows developers to extract text information from images, which can be particularly useful for recognizing typewritten text on an image.

The effectiveness of OCR can be dependent on factors like text size, color, and font style, so it's recommended to use UI automation as much as possible and resort to OCR only when necessary.

## Prerequisites

    - Cadmus device?
    - WinAppSDK version?
    - Windows version?
    - Others?

## How does OCR work?

Here's a basic overview of how the OCR API works:

1. The app selects an input image file using a picker.
2. It loads the bitmap and processes it using OCR in the default user language to extract text information.
3. The recognized text is then outputted, which can be used for various purposes within the app.
For example, in C#, you would load the image into a `SoftwareBitmap`, and then process it using the OCR API to extract text info. The OCR API supports a wide range of languages and can recognize both printed and handwritten text

## Links to API Ref pages

TODO: List related API Ref pages (Expected publish date? Are these experimental only? When will GA be?)

## Security risks to be aware of

TODO: Anything to call out? Responsible AI page link?

## FAQs

TODO Any FAQs to cover?
