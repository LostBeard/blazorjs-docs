# BlazorJSRuntime 

### Add Nuget
[![NuGet](https://img.shields.io/nuget/dt/SpawnDev.BlazorJS.svg?label=SpawnDev.BlazorJS)](https://www.nuget.org/packages/SpawnDev.BlazorJS) 

### Example Program.cs.
- Add the BlazorJSRuntime service with builder.Services.AddBlazorJSRuntime()
- Initialize BlazorJSRuntime by calling BlazorJSRunAsync() instead of RunAsync()

```cs
...
using SpawnDev.BlazorJS;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
// add SpawnDev.BlazorJS.BlazorJSRuntime
builder.Services.AddBlazorJSRuntime();
// other services ...
// build and Init using BlazorJSRunAsync (instead of RunAsync)
await builder.Build().BlazorJSRunAsync();
```

BlazorJSRunAsync() is a scope aware replacement for RunAsync(). It provides a few features that are required by some SpawnDev services, including auto starting services that request it, and preventing Blazor from trying to render components when not running in the window scope.

### Inject into components
```cs
[Inject]
BlazorJSRuntime JS { get; set; }
```

Examples
```cs
// Get and Set
var innerHeight = JS.Get<int>("window.innerHeight");
JS.Set("document.title", "Hello World!");

// Call
var item = JS.Call<string?>("localStorage.getItem", "itemName");
JS.CallVoid("addEventListener", "resize", Callback.Create(() => Console.WriteLine("WindowResized"), _callBacks));

// Attach events
using var window = JS.Get<Window>("window");
window.OnOffline += Window_OnOffline;
```

## IMPORTANT NOTE - Async vs Sync Javascript calls
The BlazorJSRuntime behaves differently than the default Blazor JSRuntime. BlazorJSRuntime is more of a 1 to 1 mapping to Javascript. 

When calling Javascript methods that are not asynchronous and do not return a Promise you need to use the synchronous BlazorJSRuntime methods Call, CallVoid, or Get. 
Unlike the default Blazor JSRuntime which would allow the use of InvokeAsync, you must use the synchronous BlazorJSRuntime methods.

Use synchronous BlazorJSRuntime calls for synchronous Javascrtipt methods. 
BlazorJSRuntime CallAsync would throw an error if used on the below Javascript method.

```js
// Javascript
function AddNum(num1, num2){
    return num1 + num2;
}
```

```cs
// C#
var total = JS.Call<int>("AddNum", 20, 22);
// total == 42 here
```


Use asynchronous BlazorJSRuntime calls for asynchronous Javascript methods.
```js
// Javascript
async function AddNum(num1, num2){
    return num1 + num2;
}
```

```cs
// C#
var total = await JS.CallAsync<int>("AddNum", 20, 22);
// total == 42 here
```

Use asynchronous BlazorJSRuntime calls for methods that return a Promise.
```js
// Javascript
function AddNum(num1, num2){
    return new Promise((resolve, reject)=>{
        resolve(num1 + num2);
    });
}
```

```cs
// C#
var total = await JS.CallAsync<int>("AddNum", 20, 22);
// total == 42 here
```

