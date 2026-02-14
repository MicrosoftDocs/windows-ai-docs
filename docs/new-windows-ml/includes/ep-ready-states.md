Each **ExecutionProvider** has a **ReadyState** property that indicates its current status on the device. Understanding these states helps you determine what actions your app needs to take.

| ReadyState | Definition | Next steps |
|------------|------------|------------|
| `NotPresent` | The EP is not installed on the client device. | Call `EnsureReadyAsync()` to download and install the EP and add it to your app's runtime dependency graph. |
| `NotReady` | The EP is installed on the client device, but hasn't been added to the app's runtime dependency graph. | Call `EnsureReadyAsync()` to add the EP to your app's runtime dependency graph. |
| `Ready` | The EP is installed on the client device and has been added to your app's runtime dependency graph. | Call `TryRegister()` to register the EP with ONNX Runtime. |