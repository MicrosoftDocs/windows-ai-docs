> [!IMPORTANT]
> **Package Manifest Requirements**: To use Windows AI imaging APIs, your app must be packaged as an MSIX package with the `systemAIModels` capability declared in your `Package.appxmanifest`. Additionally, ensure your manifest's `MaxVersionTested` attribute is set to a recent Windows version (e.g., `10.0.26226.0` or later) to properly support the Windows AI features. Using older values may cause "Not declared by app" errors when loading the model.
>
> ```xml
> <Dependencies>
>   <TargetDeviceFamily Name="Windows.Universal" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
>   <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.26226.0" />
> </Dependencies>
> ```