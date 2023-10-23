
# Union

Use the Union<T1, T2, ...\> type with method parameters for strong typing while allowing unrelated types just like in TypeScript.

```cs
void UnionTypeTestMethod(string varName, Union<bool?, string?>? unionTypeValue)
{
    JS.Set(varName, unionTypeValue);
}

var stringValue = "Hello world!";
UnionTypeTestMethod("_stringUnionValue", stringValue);
if (stringValue != JS.Get<string?>("_stringUnionValue")) throw new Exception("Unexpected result");

var boolValue = true;
UnionTypeTestMethod("_boolUnionValue", boolValue);
if (boolValue != JS.Get<bool?>("_boolUnionValue")) throw new Exception("Unexpected result");
```
