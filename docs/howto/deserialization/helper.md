# Convert JSON to an object

## Converting a string to an object

Imagine the following class:

``` vbscript
Class Person
    Public firstName as String
    Public lastName as String
    Public age as Integer
End Class
```

You have a JSON string `{"firstName":"John","lastName":"Doe","age":42}` that you want to parse into a Person object. You can do this with:

``` vbscript
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd as Person

json = |{"firstName":"John","lastName":"Doe","age":42}|
Set jd = helper.fromJsonString(json, "Person", "deser-10")
```

## Converting a string to an array of objects

If you have the same class but want to process a JSON string `[{"firstName":"John","lastName":"Doe","age":42},{"firstName":"Jane","lastName":"Doe","age":30}]`, we can do it with very similar code:

``` vbscript
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd as Variant

json = |[{"firstName":"John","lastName":"Doe","age":42},{"firstName":"Jane","lastName":"Doe","age":30}]|
jd = helper.fromJsonString(json, "Person", "deser-10")
```

The first difference is you are returning a Variant instead of a Person. As a result, you don't need `Set` in the last line.

## Converting a JSON object to an object

If you need to manipulate the content, to extract a JSON object from the string and pass that to the helper, then we need different code. Imagine the following JSON `{"success": true, "data": {"firstName":"John","lastName":"Doe","age":42}}`. The JSON object you need to use is stored in the "data" element. The code you need is this:

``` vbscript
Dim parser as New JsonParser()
Dim json as String
Dim obj as JsonObject
Dim helper as New JsonConversionHelper()
Dim jd as Variant

json = |{"success": true, "data": {"firstName":"John","lastName":"Doe","age":42}}|
Call parser.loadFromJson(json)
Set obj = parser.getRootobject().getChild("data")
Set jd = helper.toObject(obj, "Person", "deser-10").fromJson(obj)
```

Another option is to convert the JSON object back to a String, in which case this would also work: `Set jd = helper.fromJsonString(obj.toString(true), "Person", "deser-10")`.

## Converting a JSON array to an object

Imagine the JSON to parse was `{"success": true, "data": [{"firstName":"John","lastName":"Doe","age":42},{"firstName":"Jane","lastName":"Doe","age":30}]}`. The code you need is this:

``` vbscript
Dim parser as New JsonParser()
Dim json as String
Dim obj as JsonObject
Dim helper as New JsonConversionHelper()
Dim jd as Variant

json = |{"success": true, "data": [{"firstName":"John","lastName":"Doe","age":42},{"firstName":"Jane","lastName":"Doe","age":30}]}|
Call parser.loadFromJson(json)
Set obj = parser.getRootobject().getChild("data")
jd = helper.jsonArrayToObjects(obj, "Person", "deser-10")
```

As before, we could convert the JSON object back to a String, in which case this would also work: `jd = helper.fromJsonString(obj.toString(true), "Person", "deser-10")`.

## Custom fromJson() sub

If your class has its own `fromJson()` Sub that takes a JsonObject, this will be called in preference to manually deserializing the JSON. This is an example of a custom sub:

``` vbscript
Class CustomPerson

    Public firstName as String
    Public lastName as String
    Public generatedFromJson as Boolean

    Function fromJson(source as JsonObject) as Variant
        Me.firstName = source.getChild("firstName").scalarValue
        Me.lastName = source.getChild("lastName").scalarValue
        Me.generatedFromJson = true
        Set fromJson = Me
    End Function

End Class
```

This can then be used with the following code:

``` vbscript
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd as CustomPerson

json = |{"firstName":"John","lastName":"Doe","age":42}|
Set jd = helper.fromJsonString(json, "CustomPerson", "deser-10")
Print jd.firstName & " " & jd.lastName & " - generated: " & jd.generatedFromJson
```

`age` will be ignored by the custom function and instead `generatedFromJson` will be set to true.

If the function throws an error, the normal deserialization will be run. If the function runs to completion either successfully or handling the error internally, normal deserialization won't be attempted and the object will be returned. So if an error could be thrown, the JsonConversionHelper will need to be set up with appropriate converters.

This approach can be used for deserializing a single object or an array of objects. If serializing an array, the custom `fromJson()` function will be run and the resulting complete object added to the Variant array.

[Example code](../../assets/example_code/deser-10.txt){: target="_new" rel="noopener noreferrer"}