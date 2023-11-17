# WebWorkers

### Add Nuget
[![NuGet](https://img.shields.io/nuget/dt/SpawnDev.BlazorJS.WebWorkers.svg?label=SpawnDev.BlazorJS.WebWorkers)](https://www.nuget.org/packages/SpawnDev.BlazorJS.WebWorkers) 

### Example Program.cs
```cs
...
using SpawnDev.BlazorJS;
using SpawnDev.BlazorJS.WebWorkers;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");
// Add SpawnDev.BlazorJS.BlazorJSRuntime
builder.Services.AddBlazorJSRuntime();
// Add SpawnDev.BlazorJS.WebWorkers.WebWorkerService
builder.Services.AddWebWorkerService();
// Add app services that will be called on the main thread and/or worker threads (Worker services must use interfaces)
builder.Services.AddSingleton<IFaceAPIService, FaceAPIService>();
builder.Services.AddSingleton<IMathsService, MathsService>();
// More app services
// ...
// build and Init using BlazorJSRunAsync (instead of RunAsync)
await builder.Build().BlazorJSRunAsync();
```

### WebWorker
You can use the WebWorkerService properties ```SharedWebWorkerSupported``` and ```WebWorkerSupported``` to check for support.

Example component code that uses the service IMyService in a WebWorker if supported and falls back to the Window scoped IMyService if not supported.
```cs
[Inject]
WebWorkerService workerService { get; set; }

[Inject]
IServiceProvider serviceProvider { get; set; }

// MyServiceAuto will be IMyService running in the WebWorker context if available and IMyService running in the Window context if not
IMyService MyService { get; set; }

WebWorker? webWorker { get; set; }

protected override async Task OnInitializedAsync()
{
    // GetWebWorker() will return null if workerService.WebWorkerSupported == false
    webWorker = await workerService.GetWebWorker();
    // get the WebWorker's service instance if available or this Window's service instance if not
    MyService = webWorker != null ? webWorker.GetService<IMyService>() : serviceProvider.GetService<IMyService>();
    await base.OnInitializedAsync();
}
```

Another example with a progress callback.
```cs

// Create a WebWorker

[Inject]
WebWorkerService workerService { get; set; }
 
 // ...

var webWorker = await workerService.GetWebWorker();

// Call GetService<ServiceInterface> on a web worker to get a proxy for the service on the web worker.
// GetService can only be called with Interface types
var workerMathService = webWorker.GetService<IMathsService>();

// Call async methods on your worker service 
var result = await workerMathService.CalculatePi(piDecimalPlaces);

// Action types can be passed for progress reporting
var result = await workerMathService.CalculatePiWithActionProgress(piDecimalPlaces, new Action<int>((i) =>
{
    // the worker thread can call this method to report progress if desired
    piProgress = i;
    StateHasChanged();
}));
```

### SharedWebWorker
Calling GetSharedWebWorker in another window with the same sharedWorkerName will return the same SharedWebWorker
```cs
// Create or get SHaredWebWorker with the provided sharedWorkerName
var sharedWebWorker = await workerService.GetSharedWebWorker("workername");

// Just like WebWorker but shared
var workerMathService = sharedWebWorker.GetService<IMathsService>();

// Call async methods on your shared worker service
var result = await workerMathService.CalculatePi(piDecimalPlaces);

```


## Send events
```cs
// Optionally listen for event messages
worker.OnMessage += (sender, msg) =>
{
    if (msg.TargetName == "progress")
    {
        PiProgress msgData = msg.GetData<PiProgress>();
        piProgress = msgData.Progress;
        StateHasChanged();
    }
};

// From SharedWebWorker or WebWorker threads send an event to connected parents
workerService.SendEventToParents("progress", new PiProgress { Progress = piProgress });

// Or on send an event to a connected worker
webWorker.SendEvent("progress", new PiProgress { Progress = piProgress });
```

## Worker Transferable JSObjects

[Faster is better.](https://developer.chrome.com/blog/transferable-objects-lightning-fast/) SpawnDev WebWorkers use [transferable objects](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Transferable_objects) by default for better performance, but it can be disabled with WorkerTransferAttribute. Setting WorkerTransfer to false will cause the property, return value, or parameter to be copied to the receiving thread instead of transferred.


Example
```cs
public class ProcessFrameResult : IDisposable
{
    [WorkerTransfer(false)]
    public ArrayBuffer? ArrayBuffer { get; set; }
    public byte[]? HomographyBytes { get; set; }
    public void Dispose(){
        ArrayBuffer?.Dispose();
    }
}

[return: WorkerTransfer(false)]
public async Task<ProcessFrameResult?> ProcessFrame([WorkerTransfer(false)] ArrayBuffer? frameBuffer, int width, int height, int _canny0, int _canny1, double _needlePatternSize)
{
    var ret = new ProcessFrameResult();
    // ...
    return ret;
}
```

In the above example; the WorkerTransferAttribute on the return type set to false will prevent all properties of the return type from being transferred.

### Transferable JSObject types. Source [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Transferable_objects#supported_objects)
ArrayBuffer  
MessagePort  
ReadableStream  
WritableStream  
TransformStream  
AudioData  
ImageBitmap  
VideoFrame  
OffscreenCanvas  
RTCDataChannel  