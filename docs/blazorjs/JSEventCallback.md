# JSEventCallback

Now used extensively throughout the JSObject collection, JSEventCallback allows a clean .Net style way to add and remove .Net callbacks for Javascript events.

With JSEventCallback the operands += and -= can be used to attach and detach .Net callbacks to Javascript events. All reference handling is done automatically when events are added and removed.

Example taken from the Window JSObject class which inherits from EventTarget. JSEventCallback handles creating and disposing of the Callback object passed to Javascript.
```cs
public class Window : EventTarget
{
    ...
    // This is how JSEventCallback is implemented in the Window class
    public JSEventCallback<StorageEvent> OnStorage { get => new JSEventCallback<StorageEvent>(o => AddEventListener("storage", o), o => RemoveEventListener("storage", o)); set { /** set MUST BE HERE TO ENABLE += -= operands **/ } }
    ...
}
```

Example event attach, detach, and handler
```cs
void AttachEventHandlersExample()
{
    using var window = JS.Get<Window>("window");
    // If this is the first time Window_OnStorage has been attached to an event a .Net reference is automatically created and held for future use and removal
    window.OnStorage += Window_OnStorage;
    // the window JSObject reference can safely be disposed as the .Net reference is attached to Window_OnStorage internally
}
void DetachEventHandlersExample()
{
    using var window = JS.Get<Window>("window");
    // If this is the last reference of Window_OnStorage being removed then the .Net reference will automatically be disposed.
    // IMPORTANT - detaching is important for preventing resource leaks. .Net references are only released when the reference count reaches zero (same number of -= as += used)
    window.OnStorage -= Window_OnStorage;
}
void Window_OnStorage(StorageEvent storageEvent)
{
    Console.WriteLine($"StorageEvent: {storageEvent.Key} has changed");
}
```
