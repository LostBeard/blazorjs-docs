# IJSInProcessObjectReference extended

IJSInProcessObjectReference extensions methods have been added to aid in Javascript interop. JSObject wrapped Javascript objects use the extended IJSInProcessObjectReference reference internally. 

### IJSInProcessObjectReference extended methods:  
- CallVoid(string identifier, arg0, ...)
- Call<T>(string identifier, arg0, ...)
- CallAsync<T>(string identifier, arg0, ...)
- CallVoidAsync(string identifier, arg0, ...)
- Get<T>(int identifier)
- Get<T>(string identifier)
- GetAsync<T>(string identifier)
- Set(int identifier, object? value)
- Set(string identifier, object? value)

```cs
// Get Set
var window = JS.Get<IJSInProcessObjectReference>("window");
window.Set("myVar", 5);
var myVar = window.Get<int>("myVar");

// Synchronous calls
window.CallVoid("addEventListener", "resize", Callback.Create(() => Console.WriteLine("WindowResized")));
```

Create a new Javascript object usign the BlazorJSRuntimme (JS)
```cs
IJSInProcessObjectReference worker = JS.New("Worker", myWorkerScript);
```
