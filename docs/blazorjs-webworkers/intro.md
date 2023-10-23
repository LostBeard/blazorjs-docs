# SpawnDev.BlazorJS.WebWorkers
[![NuGet](https://img.shields.io/nuget/dt/SpawnDev.BlazorJS.WebWorkers.svg?label=SpawnDev.BlazorJS.WebWorkers)](https://www.nuget.org/packages/SpawnDev.BlazorJS.WebWorkers) 

- Supports Blazor WASM .Net 6, 7, and 8.

- Easily call Blazor Services in separate threads with WebWorkers and SharedWebWorkers 

- Does not require SharedArrayBuffer and therefore does not require the special HTTP headers associated with using it.

- Supports and uses transferable objects whenever possible

- Run Blazor WASM in a Service Worker

Tested working in the following browsers (tested with .Net 8.) Chrome Android does not currently support SharedWorkers. 

| Browser         | WebWorker Status | SharedWebWorker Status |
|-----------------|------------------|------------------------|
| Chrome          | ✔ | ✔ |
| MS Edge         | ✔ | ✔ |
| Firefox         | ✔ | ✔ | 
| Chrome Android  | ✔ | ❌ (SharedWorker not supported by browser) |
| MS Edge Android | ✔ | ❌ (SharedWorker not supported by browser) |
| Firefox Android | ✔ | ✔ | 

Firefox WebWorkers note:  
Firefox does not support dynamic modules in workers, which originally made BlazorJS.WebWorkers fail in that browser.
The web worker script now tries to detect this and changes the blazor wasm scripts before they are loaded to workaround this limitation. It is possible some other browsers may have this issue but may not be detected properly.

Issues can be reported [here](https://github.com/LostBeard/SpawnDev.BlazorJS/issues) on GitHub.
