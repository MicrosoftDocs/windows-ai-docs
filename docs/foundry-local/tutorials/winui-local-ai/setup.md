---
title: Local AI tutorial - Set up the note editor
description: Create a WinUI 3 project and configure Foundry Local with copy-and-paste code, then prepare to add summarization to your app.
author: GrantMeStrength
ms.author: jken
ms.date: 09/21/2026
ms.topic: tutorial
---

# Set up the note editor

Create a small WinUI 3 project, then copy the code from these topics into it. You don't need to clone a repository or download a complete sample. If you already have a WinUI 3 app, adapt the configuration below and retain your app's existing identity, assets, and navigation.

## Create your project

Follow the [command-line WinUI setup](/windows/apps/get-started/start-here?tabs=command-line) to install the .NET 10 SDK and WinUI templates. From a directory where you keep projects, create a project named **LocalNotes**:

```powershell
dotnet new winui-mvvm -n LocalNotes --dotnetVersion net10.0 --targetPlatformMinVersion 10.0.26100.0
Set-Location LocalNotes
```

The **WinUI MVVM App** template supplies the app manifest, assets, page, view model, and architecture-specific publish profiles. The project name matters because the code in this tutorial uses the `LocalNotes` namespace.

You can open the generated `LocalNotes.csproj` in Visual Studio 2026 or continue in your editor. Select **ARM64** on an Arm64 PC or **x64** on an Intel or AMD PC. Don't select **Any CPU**: the inference runtime includes architecture-specific native libraries.

## Configure the project

For the new LocalNotes project, replace the contents of **LocalNotes.csproj** with the following code. Keep the template-generated `Assets` and `Properties\PublishProfiles` folders, `app.manifest`, and `Package.appxmanifest`.

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net10.0-windows10.0.26100.0</TargetFramework>
    <TargetPlatformMinVersion>10.0.26100.0</TargetPlatformMinVersion>
    <RootNamespace>LocalNotes</RootNamespace>
    <ApplicationManifest>app.manifest</ApplicationManifest>
    <Platforms>ARM64;x64</Platforms>
    <Platform Condition="'$(Platform)' == '' or '$(Platform)' == 'AnyCPU'">ARM64</Platform>
    <RuntimeIdentifiers>win-arm64;win-x64</RuntimeIdentifiers>
    <PublishProfile>win-$(Platform).pubxml</PublishProfile>
    <UseWinUI>true</UseWinUI>
    <WinUISDKReferences>false</WinUISDKReferences>
    <EnableMsixTooling>true</EnableMsixTooling>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

  <ItemGroup>
    <Content Include="Assets\SplashScreen.scale-200.png" />
    <Content Include="Assets\LockScreenLogo.scale-200.png" />
    <Content Include="Assets\Square150x150Logo.scale-200.png" />
    <Content Include="Assets\Square44x44Logo.scale-200.png" />
    <Content Include="Assets\Square44x44Logo.targetsize-24_altform-unplated.png" />
    <Content Include="Assets\Square44x44Logo.targetsize-48_altform-lightunplated.png" />
    <Content Include="Assets\StoreLogo.png" />
    <Content Include="Assets\AppIcon.ico" />
    <Content Include="Assets\Wide310x150Logo.scale-200.png" />
  </ItemGroup>

  <ItemGroup>
    <Manifest Include="$(ApplicationManifest)" />
  </ItemGroup>

  <!--
    Defining the "Msix" ProjectCapability here allows the Single-project MSIX Packaging
    Tools extension to be activated for this project even if the Windows App SDK Nuget
    package has not yet been restored.
  -->
  <ItemGroup Condition="'$(DisableMsixProjectCapabilityAddedByProject)'!='true' and '$(EnableMsixTooling)'=='true'">
    <ProjectCapability Include="Msix" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AI.Foundry.Local.WinML" Version="1.2.4" />
    <PackageReference Include="Microsoft.Windows.SDK.BuildTools" Version="10.0.28000.2705" />
    <PackageReference Include="Microsoft.WindowsAppSDK" Version="2.4.0" />
    <PackageReference Include="CommunityToolkit.Mvvm" Version="8.4.2" />
  </ItemGroup>

  <!--
    Defining the "HasPackageAndPublishMenuAddedByProject" property here allows the Solution
    Explorer "Package and Publish" context menu entry to be enabled for this project even if
    the Windows App SDK Nuget package has not yet been restored.
  -->
  <PropertyGroup Condition="'$(DisableHasPackageAndPublishMenuAddedByProject)'!='true' and '$(EnableMsixTooling)'=='true'">
    <HasPackageAndPublishMenu>true</HasPackageAndPublishMenu>
  </PropertyGroup>

  <!-- Publish Properties -->
  <PropertyGroup>
    <PublishReadyToRun Condition="'$(Configuration)' == 'Debug'">False</PublishReadyToRun>
    <PublishReadyToRun Condition="'$(Configuration)' != 'Debug'">True</PublishReadyToRun>
    <PublishTrimmed Condition="'$(Configuration)' == 'Debug'">False</PublishTrimmed>
    <PublishTrimmed Condition="'$(Configuration)' != 'Debug'">False</PublishTrimmed>
  </PropertyGroup>

  <!-- Foundry Core 1.2.4 bundles an older copy of this DLL as well as depending
       on the WinML package. Keep the dependency's DLL once in the MSIX payload. -->
  <Target Name="RemoveDuplicateFoundryWinML" AfterTargets="ResolvePackageAssets">
    <ItemGroup>
      <NativeCopyLocalItems Remove="@(NativeCopyLocalItems)"
          Condition="'%(NativeCopyLocalItems.NuGetPackageId)' == 'Microsoft.AI.Foundry.Local.Core.WinML' and '%(NativeCopyLocalItems.Filename)%(NativeCopyLocalItems.Extension)' == 'Microsoft.Windows.AI.MachineLearning.dll'" />
    </ItemGroup>
  </Target>
</Project>
```

The validated versions are [Microsoft.AI.Foundry.Local.WinML 1.2.4](https://www.nuget.org/packages/Microsoft.AI.Foundry.Local.WinML/1.2.4), [Microsoft.WindowsAppSDK 2.4.0](https://www.nuget.org/packages/Microsoft.WindowsAppSDK/2.4.0), and [CommunityToolkit.Mvvm 8.4.2](https://www.nuget.org/packages/CommunityToolkit.Mvvm/8.4.2). Use the versions shown here rather than upgrading packages while following the walkthrough.

> [!NOTE]
> With these package versions, the Foundry Core package and its Windows ML dependency both supply `Microsoft.Windows.AI.MachineLearning.dll`. The `RemoveDuplicateFoundryWinML` target removes only the redundant Foundry copy from the package assets, retaining the dependency's copy. Without it, a packaged build can fail with `APPX1101`. Reevaluate this version-specific target when updating dependencies.

Keep `Package.appxmanifest` and the packaged launch configuration. The project-creation command sets the minimum Windows version to build 26100. You don't need the `systemAIModels` capability or a Phi Silica access token for this Foundry Local feature.

## Build and open the app

In Visual Studio, select the **LocalNotes (Package)** launch profile and press <kbd>F5</kbd>. If you use a terminal with the .NET 10 SDK and [winapp](/windows/apps/dev-tools/winapp-cli/) installed, build and launch for your architecture:

```powershell
dotnet build LocalNotes.csproj -c Debug -p:Platform=ARM64
winapp run .\bin\ARM64\Debug\net10.0-windows10.0.26100.0\win-arm64 --detach --json
```

On an x64 PC, replace `ARM64` with `x64` and `win-arm64` with `win-x64`. `winapp run` launches the app with package identity; don't launch the built executable directly. These are local development commands, not instructions for producing a signed distributable MSIX.

At this checkpoint, the app still shows the template's starter page. Confirm that it opens, then close it before editing the files in the next topic. You haven't added the note editor or model-setup controls yet, and no model download should occur.

> [!NOTE]
> NuGet restore downloads application dependencies during the build. The separate model-setup action in the running app downloads model files. These are different downloads with different purposes.

## Add the dependency to an existing app

If you're adapting an existing app instead of creating LocalNotes, first build and run your app without AI. Then, from the directory containing its C# project, add the Windows-specific SDK:

```powershell
dotnet add package Microsoft.AI.Foundry.Local.WinML
```

The tutorial uses the MVVM Toolkit for observable properties and asynchronous commands. Add it if your project doesn't already reference it:

```powershell
dotnet add package CommunityToolkit.Mvvm
```

Compare the project configuration above with your app's target framework, supported Windows version, and package references. Merge the relevant settings rather than replacing your existing project's identity, assets, signing settings, or navigation.

The versionless commands select the current stable packages. To reproduce this walkthrough exactly, use the package versions in the XML above instead. When using Foundry Local WinML 1.2.4 in a packaged app, also retain the narrowly scoped duplicate-asset target shown above.

This tutorial uses the native in-process SDK. You don't install the Foundry Local CLI, start its service, choose a localhost port, or configure an OpenAI cloud endpoint. The `Betalgo.Ranul.OpenAI` message types used in the service arrive as a transitive SDK dependency; their namespace doesn't mean the sample calls a cloud service. For other integration choices, see the [Foundry Local SDK reference](/azure/foundry-local/reference/reference-sdk-current).

## Identify the feature boundary

The note is the input; the summary is a separate output. Model setup happens after a user action, and generation runs only after setup succeeds. Your app still works as a text editor if the model isn't ready.

In the next step, you create the service and replace the starter view model and page. Each code block contains the complete contents of its named file, including namespaces and using directives. Copy all the files before building again so the page, commands, and window lifetime are connected.

> [!div class="nextstepaction"]
> [Add local summarization](add-ai.md)
