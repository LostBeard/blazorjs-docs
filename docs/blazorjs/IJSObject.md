# IJSObject Interface
SpawnDev.BlazorJS can now wrap Javascript objects using interfaces. Just like objects derived from the JSObject class, IJSObject interfaces internally use IJSInProcessObjectReference to wrap a Javascript object for direct manipulation and can be passed to and from Javascript. The main difference is IJSObjects use DispatchProxy to implement the desired interface at runtime instead of requiring a type that inherits JSObject. Currently SpawnDev.BlazorJS does not provide any interfaces for Javascript objects or apis but interfaces are simple to set up.

IJSObject Example
```cs
// create an interface for your Javascript object that implements IJSObject
public interface IWindow : IJSObject 
{
    string Name { get; set; }
    void Alert(string msg = "");
    // ...
}

// use your IJSObject interface to interact with the Javascript object
public void IJSObjectInterfaceTest() {
    var w = JS.Get<IWindow>("window");
    var randName = Guid.NewGuid().ToString();
    // directly set the window.name property
    w.Name = randName;
    // verify the read back
    if (w.Name != randName) throw new Exception("Interface property set/get failed");
}
```
