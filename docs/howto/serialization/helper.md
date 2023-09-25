# Convert an object to JSON

## Converting an object to a string

Imagine the following class:

``` vbscript
Class Person
    Public firstName as String
    Public lastName as String
    Public age as Integer
End Class
```

You want to return the JSON as a non-prettified JSON String, so you call `toJsonString()` passing the Person object and `false` as the second parameter on line 11.

``` vbscript linenums="1"
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd as New Person()

jd.firstName = "John"
jd.lastName = "Doe"
jd.age = 42
json = helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withScalarConverter("age")._
    toJsonString(jd, false)
```

This returns `{"firstName":"John","lastName":"Doe","age":42}`.

The same syntax can be used to convert an object or an array of objects.

## Converting an object to a JsonObject

With the same class, if you want to return a JsonObject instead of the string of JSON, you would instead use:

``` vbscript
Dim jsonObj as JsonObject
Dim helper as New JsonConversionHelper()
Dim jd as New Person()

jd.firstName = "John"
jd.lastName = "Doe"
jd.age = 42
Set jsonObj = helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withScalarConverter("age")._
    toJson(jd)
```

## Converting an array of objects

If you have the same class but want to process multiple objects, you can do it with a similar code:

``` vbscript linenums="1"
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd(1) as Person

Set jd(0) = New Person()
jd(0).firstName = "John"
jd(0).lastName = "Doe"
jd(0).age = 42
Set jd(1) = New Person()
jd(1).firstName = "Jane"
jd(1).lastName = "Doe"
jd(1).age = 30
json = helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withScalarConverter("age")._
    toJsonString(jd, true)
```

You could pass in a Person array, as here, or a Variant containing the Person array. If you wanted a JsonObject, you would just call `helper.toJson(jd)` on line 16. But this code returns a prettified JSON string:

```json
[
    {
        "firstName": "John",
        "lastName": "Doe",
        "age": 42
    },
    {
        "firstName": "Jane",
        "lastName": "Doe",
        "age": 30
    }
]
```

## Using provided converters for different properties

As with deserialization, it's possible to serialize from one property name and write to a different label in the JSON. In this case, you need to pass the `JsonScalarConverter` as a **JsonCustomConverter**. If you want to get the `lastName` property and write it to a `surname` element in the JSON, the code would be:

```vbscript
Dim surnameConverter as New JsonScalarConverter
Set jsonObj = helper.withScalarConverter("firstName")._
    withCustomConverter("surname", surnameConverter.forPropertyName("lastName"))._
    withScalarConverter("age")._
    toJson(jd)
```

You declare a JsonScalarConverter as `surnameConverter` in line 1, and then pass it to the helper for the property name "lastName" in line 3 to write to the label "surname".

## Custom toJson() function

If your class has its own `toJson()` function that returns a JsonObject, this will be called in preference to manually serializing the object. This is an example of a custom function

```vbscript
Class CustomPerson

    Public firstName as String
    Public lastName as String

    Function toJson() as JsonObject
        Dim resp as New JsonObject()
        Call resp.insertValue("firstName", Me.firstName)
        Call resp.insertValue("lastName", Me.lastName)
        Call resp.insertValue("generated", true)
        Set toJson = resp
    End Function

End Class
```

With a custom serialization function, there is no need to pass any converters to the helper. So the code required will be:

```vbscript
    Dim jsonObj as JsonObject
    Dim helper as New JsonConversionHelper()

    Set jsonObj = helper.toJson(jd)
```

If the function throws an error, the normal serialization will be run. If the function runs to completion either successfully or handling the error internally, normal deserialization won't be attempted and the object will be returned. So if an error could be thrown, the JsonConversionHelper will need to be set up with appropriate converters.

This approach can be used for serializing a single object or an array of objects. If serializing an array, the custom `fromJson()` function will be run and the resulting complete object added to the JsonObject array.

[Example code](../../assets/example_code/ser-10.txt){: target="_new" rel="noopener noreferrer"}
