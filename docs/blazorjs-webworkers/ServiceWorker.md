# ServiceWorker
As of version 2.2.21 SpawnDev.BlazorJS.WebWorkers supports running Blazor WASM apps in ServiceWorkers. Your app can now register a class to run in the ServiceWorker to handle ServiceWorker events.

### wwwroot/service-worker.js
Create or modify to match the line below.
```js
importScripts('_content/SpawnDev.BlazorJS.WebWorkers/spawndev.blazorjs.webworkers.js');
```

### Program.cs
A minimal Program.cs
```cs
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");
// SpawnDev.BlazorJS
builder.Services.AddBlazorJSRuntime();
// SpawnDev.BlazorJS.WebWorkers
builder.Services.AddWebWorkerService();
// Register a ServiceWorker handler (AppServiceWorker here) that inherits from ServiceWorkerEventHandler
builder.Services.RegisterServiceWorker<AppServiceWorker>();
// Or Unregister the ServiceWorker if no longer desired
//builder.Services.UnregisterServiceWorker();
// SpawnDev.BlazorJS startup (replaces RunAsync())
await builder.Build().BlazorJSRunAsync();
```

### AppServiceWorker.cs
A verbose service worker implementation example.
- Handle ServiceWorker events by overriding the ServiceWorkerEventHandler base class virtual methods.
- The ServiceWorker event handlers are only called when running in a ServiceWorkerGlobalScope context.
- The AppServiceWorker singleton may be started in any scope and therefore must be scope aware. (For example, do not try to use localStorage in a Worker scope.)
```cs
public class AppServiceWorker : ServiceWorkerEventHandler
{
    public AppServiceWorker(BlazorJSRuntime js, ServiceWorkerConfig serviceWorkerConfig) : base(js, serviceWorkerConfig)
    {

    }

    // called before any ServiceWorker events are handled
    protected override async Task OnInitializedAsync()
    {
        // This service may start in any scope. This will be called before the app runs.
        // If JS.IsWindow == true be careful not stall here.
        // you can do initialization based on the scope that is running
        Log("GlobalThisTypeName", JS.GlobalThisTypeName);
    }

    protected override async Task ServiceWorker_OnInstallAsync(ExtendableEvent e)
    {
        Log($"ServiceWorker_OnInstallAsync");
        _ = ServiceWorkerThis!.SkipWaiting();   // returned task can be ignored
    }

    protected override async Task ServiceWorker_OnActivateAsync(ExtendableEvent e)
    {
        Log($"ServiceWorker_OnActivateAsync");
        await ServiceWorkerThis!.Clients.Claim();
    }

    protected override async Task<Response> ServiceWorker_OnFetchAsync(FetchEvent e)
    {
        Log($"ServiceWorker_OnFetchAsync", e.Request.Method, e.Request.Url);
        Response ret;
        try
        {
            ret = await JS.Fetch(e.Request);
        }
        catch (Exception ex)
        {
            ret = new Response(ex.Message, new ResponseOptions { Status = 500, StatusText = ex.Message, Headers = new Dictionary<string, string> { { "Content-Type", "text/plain" } } });
            Log($"ServiceWorker_OnFetchAsync failed: {ex.Message}");
        }
        return ret;
    }

    protected override async Task ServiceWorker_OnMessageAsync(ExtendableMessageEvent e)
    {
        Log($"ServiceWorker_OnMessageAsync");
    }

    protected override async Task ServiceWorker_OnPushAsync(PushEvent e)
    {
        Log($"ServiceWorker_OnPushAsync");
    }

    protected override void ServiceWorker_OnPushSubscriptionChange(Event e)
    {
        Log($"ServiceWorker_OnPushSubscriptionChange");
    }

    protected override async Task ServiceWorker_OnSyncAsync(SyncEvent e)
    {
        Log($"ServiceWorker_OnSyncAsync");
    }

    protected override async Task ServiceWorker_OnNotificationCloseAsync(NotificationEvent e)
    {
        Log($"ServiceWorker_OnNotificationCloseAsync");
    }

    protected override async Task ServiceWorker_OnNotificationClickAsync(NotificationEvent e)
    {
        Log($"ServiceWorker_OnNotificationClickAsync");
    }
}
```