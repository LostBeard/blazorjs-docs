# Callback

The Callback class is used to support Action and Func serialization. Callback.Create and Callback.CreateOne are shortcut methods for creating ActionCallback, FuncCallback, AsyncActionCallback, and AsyncFuncCallback.

Pass lambda callbacks to Javascript
```cs
JS.Set("testCallback", Callback.Create<string>((strArg) => {
    Console.WriteLine($"Javascript sent: {strArg}");
    // this prints "Hello callback!"
}));
```
```js
// in Javascript
testCallback('Hello callback!');
```

Pass method callbacks to Javascript
```cs
string SomeNetFn(string input){
    return $"Recvd: {input}";
}

JS.Set("someNetFn", Callback.CreateOne<string, string>(SomeNetFn));
```
```js
// in Javascript
someNetFn('Hello callback!');

// prints
Recvd: Hello callback!
```

Pass async method callbacks to Javascript
Under the hood, BlazorJS is returning a Promise to Javascript when the method is called

```cs
async Task<string> SomeNetFnAsync(string input){
    return $"Recvd: {input}";
}

JS.Set("someNetFnAsync", Callback.CreateOne<string, string>(SomeNetFnAsync));
```
```js
// in Javascript
await someNetFnAsync('Hello callback!');

// prints
Recvd: Hello callback!
```
