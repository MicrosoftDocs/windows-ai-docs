---
title: Distribute models for Windows ML
description: Learn how to bundle an ONNX model with your app or download it separately, with implementation guidance for each approach.
author: GrantMeStrength
ms.author: jken
ms.topic: overview
ms.date: 09/01/2026
---

# Distribute models for Windows ML

Windows ML loads a model from a local file path, so it doesn't require any particular distribution method. Your app either ships the model as part of its package, or acquires the model separately — downloaded in your installer, or by your app the first time it's needed. This article compares the two approaches and walks through implementing each.

## Choose an approach

| | Include the model in your package | Download the model separately |
|---|---|---|
| **Best for** | Small models, or models that must work offline immediately after install | Large models, or models you want to distribute separately from your app |
| **First-run experience** | Model is available immediately | Requires a download before first use, unless you download in your installer or prefetch in the background |
| **App size impact** | Increases installed size for every user | Only uses storage on devices where a user opts into the feature that needs the model |
| **Offline support** | Always available offline | Only available offline after the first successful download |

If your model is a few megabytes and rarely changes, bundling it is simpler and gives users a working app immediately. If your model is large, updated frequently, or optional for some users, download it separately instead.

> [!TIP]
> If you're publishing a library or SDK that multiple independent apps depend on, and those apps should share a single on-disk copy of the model, use the [Windows ML Model Catalog APIs](#share-a-downloaded-model-across-apps) instead of writing your own download-and-share logic. Most app developers who only need a model for their own app should use one of the two approaches described here.

## Include the model in your package

Add the ONNX model file, along with any files it depends on such as tokenizer or label files, as content in your app project. Windows ML then loads the model directly from your app's install location, with no network access required.

### Implementation steps

1. Add the model file to your project and set its **Build Action** to **Content** (or the equivalent for your project type) so it's copied to the output and included in the app package.
2. Resolve the model's path relative to your app's install location at run time. Don't hard-code an absolute path, because the install location varies by user and deployment type.
3. Load the model from that path using your inference APIs.

```csharp
using System;
using System.IO;

// Resolve the model path relative to the app's install location.
string modelPath = Path.Combine(AppContext.BaseDirectory, "Assets", "Models", "model.onnx");

if (!File.Exists(modelPath))
{
    throw new FileNotFoundException("The bundled model is missing from the app package.", modelPath);
}

// Load modelPath with your inference APIs.
```

### Considerations

- **Package size**: Every install carries the full model, which increases download size and disk usage for all users, including those who might never use the feature.
- **No download failure handling needed**: Because the model is already on disk when the app launches, you don't need retry, integrity-check, or offline-fallback logic.
- **Optional packages**: If you want to keep the model out of the base install but still avoid writing download logic, you can ship it in an optional MSIX package that users or your app installs on demand. This keeps the base package small while avoiding a custom download path, at the cost of relying on package deployment instead of a plain download.

## Download the model separately

Instead of bundling the model, you download it to the device separately from your app package and store it in app-local storage. This keeps your package small. The download doesn't have to happen from your running app — you can download the model from your app on first run, or have your installer download it as part of setup. Either way, you're responsible for downloading, verifying, storing, and cleaning up the file.

### Implementation steps

1. Host the model file (and any dependent files) at an HTTPS URL, along with its SHA-256 hash so you can verify the download.
2. Decide when the download happens: in your installer, or from your app the first time it needs the model. Either approach uses the same download-and-verify logic.
3. Check whether the model already exists in local storage before downloading it again.
4. Save the file to a writable, app-local folder, and verify its hash before use.
5. Report download progress to the user, and handle network failures so the download can retry or fall back gracefully.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Security.Cryptography;
using System.Threading;
using System.Threading.Tasks;

public static async Task<string> GetModelPathAsync(
    Uri modelUri,
    string expectedSha256,
    IProgress<double>? progress = null,
    CancellationToken cancellationToken = default)
{
    string modelsFolder = Path.Combine(
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData),
        "Contoso", "Models");
    Directory.CreateDirectory(modelsFolder);

    string modelPath = Path.Combine(modelsFolder, Path.GetFileName(modelUri.LocalPath));

    // Reuse the file if it's already downloaded and passes integrity verification.
    if (File.Exists(modelPath) && await VerifyHashAsync(modelPath, expectedSha256, cancellationToken))
    {
        return modelPath;
    }

    string tempPath = modelPath + ".download";

    using (var httpClient = new HttpClient())
    using (var response = await httpClient.GetAsync(
        modelUri, HttpCompletionOption.ResponseHeadersRead, cancellationToken))
    {
        response.EnsureSuccessStatusCode();

        long? totalBytes = response.Content.Headers.ContentLength;
        long bytesRead = 0;

        using var contentStream = await response.Content.ReadAsStreamAsync(cancellationToken);
        using var fileStream = File.Create(tempPath);

        var buffer = new byte[81920];
        int read;
        while ((read = await contentStream.ReadAsync(buffer, cancellationToken)) > 0)
        {
            await fileStream.WriteAsync(buffer.AsMemory(0, read), cancellationToken);
            bytesRead += read;

            if (totalBytes.HasValue)
            {
                progress?.Report((double)bytesRead / totalBytes.Value);
            }
        }
    }

    if (!await VerifyHashAsync(tempPath, expectedSha256, cancellationToken))
    {
        File.Delete(tempPath);
        throw new InvalidOperationException("Downloaded model failed integrity verification.");
    }

    File.Move(tempPath, modelPath, overwrite: true);
    return modelPath;
}

private static async Task<bool> VerifyHashAsync(
    string filePath, string expectedSha256, CancellationToken cancellationToken)
{
    using var sha256 = SHA256.Create();
    using var fileStream = File.OpenRead(filePath);
    byte[] hash = await sha256.ComputeHashAsync(fileStream, cancellationToken);
    string actualSha256 = Convert.ToHexString(hash);
    return string.Equals(actualSha256, expectedSha256, StringComparison.OrdinalIgnoreCase);
}
```

### Considerations

- **Integrity verification**: Always verify a SHA-256 hash (or similar) after downloading, before you use the model or treat it as cached. Otherwise a partial or corrupted download can silently fail at inference time.
- **Atomic writes**: Download to a temporary file and rename it into place only after verification succeeds, so a failed or interrupted download never leaves a corrupt file at the expected path.
- **Retry and offline handling**: Handle network errors explicitly. Decide whether your app can run in a reduced-functionality mode, prompt the user to retry, or block the feature until the download succeeds.
- **Storage location**: Use an app-local, writable folder that survives across app updates but is cleaned up if the app is uninstalled.
- **Cleanup**: If you replace the model with a new version, delete the old file so you don't accumulate unused files on the user's device.
- **Download in your installer vs. first run**: If your installer supports running a custom action or script, it can download the model as part of installation so it's ready before the user launches the app for the first time. This trades a longer install time for a better first-run experience, and it uses the same download-and-verify logic as downloading from the running app.

## Share a downloaded model across apps

If you're building a library or SDK that multiple independent apps depend on, and you want those apps to share one on-disk copy of the model instead of each downloading their own, use the Windows ML Model Catalog APIs instead of writing your own download logic. For more info, see [Get started with the Model Catalog APIs](./model-catalog/get-started.md).

## Next steps

- [Share models across apps](./model-catalog/overview.md)
- [Find or train models](./models.md)
- [Compile models for execution providers](./model-compilation.md)

