# SpawnDev.BlazorJS

[![NuGet](https://img.shields.io/nuget/dt/SpawnDev.BlazorJS.svg?label=SpawnDev.BlazorJS)](https://www.nuget.org/packages/SpawnDev.BlazorJS) 

The primary purpose of BlazorJS is to allow working with Javascript in Blazor WASM by writing C# not Javascript. Full Blazor WebAssembly and Javascript interop. Access properties, methods, events, and constructors of Javascript objects with Blazor WASM without writing Javascript.

### Primary parts of SpawnDev.BlazorJS
- BLazorJSRuntime service - The core of BlazorJS, BLazorJSRuntime is a JSRuntime alternative designed to make working with Javascript as straightforward as possible.
- JSObject - JSObject is an alternative to IJSObjectReference and IJSInProcessObjectReference that, when inherited, allows wrapping a Javascript object for use in Blazor WASM with full access to properties, methods, events, and constructors.
- Callback - Callback is used to pass .Net methods to Javascript.

### Supported .Net Versions
- Blazor WebAssembly .Net 6, 7, and 8 
- - Tested VS Template: Blazor WebAssembly Standalone App
- Blazor United .Net 8 (in WebAssembly project only) 
- - Tested VS Template: Blazor Web App (Auto or WebAssembly interactive mode)

### Features
- 260+ strongly typed JSObject wrappers ([listed here](https://blazorjs.spawndev.com/JSObjectTypeInfo)) included in BlazorJS including DOM, Crypto, WebGL, WebRTC, and Promises allow direct interaction with Javascript
- Use Javascript libraries in Blazor without writing any Javascript code
- BlazorJSRuntime wraps the default JSRuntime adding additional functionality
- Create new Javascript objects directly from Blazor
- Get and set Javascript object properties as well as access methods
- Easily pass .Net methods to Javascript using JSEventCallback, Callback.Create or Callback.CreateOne methods
- 2 options for wrapping your Javascript objects for direct manipulation from Blazor (No javascript required!)
- - Create a class that inherits JSObject and defines the methods, properties, events, and constructors of your Javascript object (best option)
- - Create an interface that implements IJSObject and defines the methods and properties of your Javascript object (more limited than JSObject option)
- Use SpawnDev.BlazorJS.WebWorkers to enable calling Blazor services in SharedWorkers and DedicatedWorkers
- Run Blazor WASM in ServiceWorkers to handle ServiceWorker events in .Net
- Supports Promises, Union method parameters, passing undefined to Javascript, and more

# Issues and Feature requests
If you find a bug or missing properties, methods, or Javascript objects please submit an issue [here](https://github.com/LostBeard/SpawnDev.BlazorJS/issues) on GitHub. I will help as soon as possible.
