---
hide:
    - toc
---
# Convert a JSON string to a variant array

Imagine you have a JSON object that has an element `"products": "Connections,Domino,Volt"`. But your class has `Public products as Variant`, intended to hold an array of products. So your custom converter needs to take the string and split it on the comma.

First, create your custom converter class:

``` vbscript
Class StringToArraySerializer as AbstractJsonConverter

End Class
```

You don't need to add a constructor - nothing changes from the base constructor. You just need to add the `fromJson()` function. This needs the same signature as the function in the base class, so `Function fromJson(source as JsonObject) as Variant`. The source being passed in will be `"products": "Connections,Domino,Volt"`.

For the body of the function, get the string value from this "products" element and convert it to an array. You get the value using `source.scalarValue`. Use `Split()` to split the string into an array, based on the separator `,`. So the complete function is:

``` vbscript
Function fromJson(source as JsonObject) as Variant
    fromJson = Split(source.scalarValue, ",")
End Function
```

Use it with the following code:

``` vbscript
Dim helper as New JsonConversionHelper
Dim strToArrSer as New StringToArraySerializer()
Call helper.withCustomConverter("products", strToArrSer)
```

[Example code](../../assets/example_code/deser-60.txt){: target="_new" rel="noopener noreferrer"}
