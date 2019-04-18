---
layout: LandingPage
author: eliotcowley
title: Windows AI
description: Transform your Windows application with the power of AI.
ms.author: elcowle
ms.date: 4/17/2019
ms.topic: landing-page
keywords: windows 10, windows ai
ms.localizationpriority: medium
---

# Windows AI

Transform your Windows application with the power of artificial intelligence. Windows AI empowers you and your business to achieve more by providing intelligent solutions to complex problems.

<br/>

<div style="text-align:center">
    <img src="images/windows-ai-photo.png" alt="AI on farm"/>
</div>

<ul class="cardsK panelContent">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <a href="windows-ml/index.md">
                                <img src="https://docs.microsoft.com/media/hubs/windows/windows-ai.svg"
                                    alt="WinML graphic"
                                    data-linktype="external"
                                    class="x-hidden-focus"/>
                            </a>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>
                            <a href="windows-ml/index.md">Windows Machine Learning</a>
                        </h3>
                        <p>Learn about how to integrate trained machine learning models into your Windows apps.</p>
                        <br/>
                        <ul>
                            <li><a href="windows-ml/get-started-uwp.md">Tutorial: Create a WinML UWP app (C#)</a></li>
                            <li><a href="windows-ml/what-is-a-machine-learning-model.md">What is a machine learning model?</a></li>
                            <li><a href="windows-ml/api-reference.md">WinML API reference</a></li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <a href="windows-vision-skills/index.md">
                                <img src="https://docs.microsoft.com/media/hubs/cortana/cortana-get-started-build-skill-language.svg" alt="Windows Vision Skills graphic" data-linktype="external" class="x-hidden-focus"/>
                            </a>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>
                            <a href="windows-vision-skills/index.md">Windows Vision Skills</a>
                        </h3>
                        <p>Abstract away the complexities of computer vision with skills.</p>
                        <br/>
                        <ul>
                            <li><a href="windows-vision-skills/tutorial.md">Tutorial: Create your own vision skill (C#)</a></li>
                            <li><a href="windows-vision-skills/tutorial1.md">Tutorial: Create a Windows Vision Skill UWP app (C#)</a></li>
                            <li><a href="windows-vision-skills/important-api-concepts.md">Important API concepts</a></li>
                            <li><a href="windows-vision-skills/samples.md">Windows Vision Skills samples</a></li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <a href="https://docs.microsoft.com/windows/desktop/direct3d12/dml">
                                <img src="https://docs.microsoft.com/media/hubs/cortana/cortana-resources-samples.svg" alt="DirectML graphic" data-linktype="external" class="x-hidden-focus"/>
                            </a>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>
                            <a href="https://docs.microsoft.com/windows/desktop/direct3d12/dml">Direct Machine Learning (DirectML)</a>
                        </h3>
                        <p>Implement high-performance machine learning using low-level, DirectX-style APIs.</p>
                        <br/>
                        <ul>
                            <li><a href="https://docs.microsoft.com/windows/desktop/direct3d12/dml-intro">Introduction to DirectML</a></li>
                            <li><a href="https://docs.microsoft.com/windows/desktop/direct3d12/dml-binding">Binding in DirectML</a></li>
                            <li><a href="https://docs.microsoft.com/windows/desktop/direct3d12/dml-min-app">DirectML sample applications</a></li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>

## Which solution is right for me?

Microsoft offers several different AI solutions, which means you have several options at your disposal. But how do you choose which one to use for your application? Let's break it down.

### I want to integrate a machine learning model into my application and run it on the device by taking full advantage of hardware acceleration

[Windows Machine Learning](windows-ml/index.md) is the right choice for you. These high-level WinRT APIs work on Windows 10 applications (UWP, desktop) and evaluate models directly on the device. You can even choose to take advantage of the device's GPU (if it has one) for better performance.

### I want to integrate computer vision into my application and take advantage of platform optimizations

[Windows Vision Skills](windows-vision-skills/index.md) is the way to go. This simple framework allows you to build custom vision applications that leverage hardware acceleration on edge devices. You can combine pre-built libraries to accomplish common image processing tasks and ML models for specialized tasks.

### I want to have fuller control over resource utilization during model execution for high-intensive applications

[DirectML](https://docs.microsoft.com/windows/desktop/direct3d12/dml) is what you want. These DirectX-style APIs provide a programming paradigm that will feel familiar to C++ game developers, and allow you to take full advantage of the hardware.

### I want to train, test, and deploy ML models with a framework that is familiar to a .NET developer

Check out [ML.NET](https://dotnet.microsoft.com/apps/machinelearning-ai/ml-dotnet), a machine learning framework built for .NET developers.

### I want to leverage the power of the Azure cloud for training and deploying ML models

See [What are the machine learning products at Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) for a comprehensive list of the solutions available from Microsoft, including many products and services that run on Azure.
