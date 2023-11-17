# IJSInProcessObjectReference extended

IJSInProcessObjectReference extensions methods have been added to aid in Javascript interop. JSObject wrapped Javascript objects use the extended IJSInProcessObjectReference reference internally. 

### IJSInProcessObjectReference extended methods:  
"identifier", aka property key, can be any type because that is what Javascript allows.
- CallVoid(object identifier, arg0, ...)
- Call<T>(object identifier, arg0, ...)
- CallAsync<T>(object identifier, arg0, ...)
- CallVoidAsync(object identifier, arg0, ...)
- Get<T>(object identifier)
- GetAsync<T>(object identifier)
- Set(object identifier, object? value)

```cs
// Get Set
var window = JS.Get<IJSInProcessObjectReference>("window");
window.Set("myVar", 5);
var myVar = window.Get<int>("myVar");

// Synchronous calls
window.CallVoid("addEventListener", "resize", Callback.Create(() => Console.WriteLine("WindowResized")));
```

Create a new Javascript object using the BlazorJSRuntime (JS)
```cs
IJSInProcessObjectReference worker = JS.New("Worker", myWorkerScript);
```
