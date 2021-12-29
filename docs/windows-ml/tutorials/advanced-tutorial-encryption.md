---
title: Use encrypted models with Windows ML
description: Learn how to use encrypted models with Windows ML
ms.date: 5/7/2021
ms.topic: article
keywords: windows 10, uwp, windows machine learning, winml, windows ML, tutorials
ms.custom: RS5
---

# Use encrypted models with Windows ML

There are several static methods we can apply on the LearningModel class to load machine learning models such as load the model from a file in your app, from a file on disk or load a model from a stream. 

Loading from a stream method allows better control over the model. In this case, you can choose to have the model encrypted on disk and decrypt it only in memory prior to calling one of the LoadFromStream methods. 

In this tutorial, you will learn how to integrate an encrypted machine learning model with Windows ML application (C#).

Windows ML APIs do not provide machine learning encryption service and will not be held responsible for any damage or loss of any kind.

## Get ONNX model

In this tutorial, you will use SqueezeNet model in ONNX format to perform the encryption, decryption and load from the stream. 

Download or clone the [SqueezeNet Object Detection sample app](https://github.com/microsoft/Windows-Machine-Learning/tree/master/Samples/SqueezeNetObjectDetection/Desktop/cpp) from GitHub to get the SqueezeNet.onnx model.

## Specify the required declarations and variables

1. Copy the below using statements declarations to get access to all the APIs that you'll need:

```csharp
using System;
using System.Threading.Tasks;
using Windows.AI.MachineLearning;
using Windows.Security.Cryptography;
using Windows.Security.Cryptography.Core;
using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
```

You'll define two special variables for a key and an initialization vector.

The *key* is a variable of the [CryptographicKey class](/uwp/api/windows.security.cryptography.core.cryptographickey?preserve-view=true&view=winrt-19041), which represents a symmetric (or an asymmetric) key pair. You will need an object from this class since you will use the `AsymmetricKeyAlgorithmProvider` method to create or import keys.

The *initialization vector* is a variable of the [IBuffer class](/uwp/api/windows.storage.streams.ibuffer?preserve-view=true&view=winrt-19041), which represents a referenced array of bytes used by byte stream read and write interfaces.

Both `CryptographicKey` and `IBuffer` class variables are used to encrypt and decrypt the stream. 

3. Add the following variable declarations and the `MainPage` class after the using statements inside the crypto namespace:

```csharp
namespace crypto
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        private CryptographicKey _key;
        private IBuffer _initialization_vector;
        public MainPage()
        {
            this.InitializeComponent();

            Run();
        }
    }
}
```

## Encrypt the Model 

Windows APIs provide a rich set of features and capabilities, which can enhance the functionality of the Windows ML API set. Here, you'll use an encryption and decryption service provided in the Windows API set to produce a stream in memory, and use Windows ML APIs to load the model from that stream.

You can use any encryption service to encrypt your machine learning model, per your convenience. In this tutorial, we'll use the encryption method – `SymmetricAlgorithmNames - .AesCbcPkcs7`.

The [SymmetricAlgorithmNames class](/uwp/api/windows.security.cryptography.core.symmetricalgorithmnames?preserve-view=true&view=winrt-19041) helps you to retrieve symmetric key algorithms to apply symmetric key encryption on your model. This type of encryption requires that the same key used for encryption also be used for decryption.

Using the `SymmetricKeyAlgorithmProvider` class, you can select an algorithm and create your key. In this tutorial, we'll use the `.AesCbcPkcs7.` algorithm.

The `AES_CBC_PKCS7` algorithm represents an advanced Encryption Standard (AES) algorithm, coupled with a cipher-block chaining mode of operation and PKCS#7 padding. 

1. The code below shows how to generate the key and encrypt the model.

```csharp
async Task<IBuffer> EncryptAsync(StorageFile model_file)
{
	// get a buffer for the model file
	var file_buffer = await Windows.Storage.FileIO.ReadBufferAsync(model_file);

	// set up the encryption algorithm
	var algorithm = SymmetricKeyAlgorithmProvider.OpenAlgorithm(SymmetricAlgorithmNames.AesCbcPkcs7);
	uint key_length = 32;
	var key_buffer = CryptographicBuffer.GenerateRandom(key_length);
	_key = algorithm.CreateSymmetricKey(key_buffer);
	_initialization_vector = CryptographicBuffer.GenerateRandom(algorithm.BlockLength);

	// perform the encryption
	var encrypted_buffer = CryptographicEngine.Encrypt(_key, file_buffer, _initialization_vector);

	return encrypted_buffer;
}
```

> [NOTE!]
> Interested in learning more about Cryptographic Keys? Please review the [Cryptographic keys documentation](/windows/uwp/security/cryptographic-keys). 

## Decrypt the Model and Load from the stream

Before loading the model, you need to decrypt it using the [CryptographicEngine.Decrypt method](/uwp/api/windows.security.cryptography.core.cryptographicengine.decrypt?preserve-view=true&view=winrt-19041). `CryptographicEngine.Decrypt` is a method to decrypt content that was previously encrypted by using a symmetric or asymmetric algorithm. When calling the method, you'll need to provide the previously generated key.

To access the decrypted model, you'll use the `InMemoryRandomAccessStream` class, which provides random access of data in input and output streams stored in memory instead of on disk. 

As the last step, you'll create a session to load the model from the stream, using the `LearningModel.LoadFromStreamAsync` method. You can call this method as a synchronous or asynchronous task.

The code below shows how to decrypt the model using the generated key, write it to a stream, and then load the model from the stream.

```csharp
async Task DecryptAndRunAsync(IBuffer encryptyed_buffer)
{
	// decrypt the buffer
	var decrypted_buffer = CryptographicEngine.Decrypt(_key, encryptyed_buffer, _initialization_vector);

	// write it to a stream
	var decrypted_stream = new InMemoryRandomAccessStream();
	await decrypted_stream.WriteAsync(decrypted_buffer);

	// load the model from the stream
	var model = await LearningModel.LoadFromStreamAsync(RandomAccessStreamReference.CreateFromStream(decrypted_stream));

	// create a session
	var session = new LearningModelSession(model);
}
```

## Run the model

Copy the following Run method to call the methods defined earlier.

```csharp
async Task Run()
{

	// get the model file
	var model_file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/SqueezeNet.onnx"));

	// encrypt the model file.
	var encryptyed_buffer = await EncryptAsync(model_file);

	// decrypt the model file and load it
	await DecryptAndRunAsync(encryptyed_buffer);
}
```

## Summary

That’s it! You have successfully loaded the model to your Windows ML app. 

After you have loaded the model, you can continue to create a session, bind model inputs and outputs, and evaluate the model to complete your Windows ML app.

### Additional Resources

To learn more about topics mentioned in this tutorial, visit the following resources:

* [Cryptographic keys documentation](/windows/uwp/security/cryptographic-keys)
* [CryptographicKey class](/uwp/api/windows.security.cryptography.core.cryptographickey?preserve-view=true&view=winrt-19041)
* [CryptographicEngine.Decrypt method](/uwp/api/windows.security.cryptography.core.cryptographicengine.decrypt?preserve-view=true&view=winrt-19041)
* [SymmetricAlgorithmNames class](/uwp/api/windows.security.cryptography.core.symmetricalgorithmnames?preserve-view=true&view=winrt-19041)
