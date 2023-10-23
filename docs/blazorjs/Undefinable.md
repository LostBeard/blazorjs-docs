
# Undefinable
Use Undefinable\<T\> type to pass undefined to Javascript  

Some Javascript API calls may have optional parameters that behave differently depending on if you pass a null versus undefined. You can now retain strong typing on JSObject method calls and support passing undefined for JSObject parameters.

New Undefinable\<T\> type. 

Example from Test app unit tests
```cs
// an example method with a parameter that can also be null or undefined
// T of Undefinable<T> must be nullable
void MethodWithUndefinableParams(string varName, Undefinable<bool?>? window)
{
    JS.Set(varName, window);
}

bool? w = false;
// test to show the value is passed normally
MethodWithUndefinableParams("_willBeDefined2", w);
bool? r = JS.Get<bool?>("_willBeDefined2");
if (r != w) throw new Exception("Unexpected result");

w = null;
// null defaults to passing as undefined
MethodWithUndefinableParams("_willBeUndefined2", w);
if (!JS.IsUndefined("_willBeUndefined2")) throw new Exception("Unexpected result");

// if you need to pass null to an Undefinable parameter use Undefinable<T?>.Null
MethodWithUndefinableParams("_willBeNull2", Undefinable<bool?>.Null);
if (JS.IsUndefined("_willBeNull2")) throw new Exception("Unexpected result");

// another way to pass undefined
MethodWithUndefinableParams("_willAlsoBeUndefined2", Undefinable<bool?>.Undefined);
if (!JS.IsUndefined("_willAlsoBeUndefined2")) throw new Exception("Unexpected result");
```

If using JSObjects you can also use JSObject.Undefined\<T\> to create an instance that will be passed to Javascript as undefined.

```cs
// Create an instance of the Window JSObject class that is revived in Javascript as undefined
var undefinedWindow = JSObject.Undefined<Window>();
// undefinedWindow is an instance of Window that is revived in Javascript as undefined
JS.Set("_undefinedWindow", undefinedWindow);
var isUndefined = JS.IsUndefined("_undefinedWindow");
// isUndefined == true here
```