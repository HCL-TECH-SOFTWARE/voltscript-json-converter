# Use object and object array converters

## Deserializing inner objects with a custom converter

Imagine the following classes:

```vbscript
Class Person

    Public firstName as String
    Public lastName as String
    Public pet as Pet

End Class

Class Pet

    Public name as String
    Public type as String

End Class
```

When deserializing, you were able to just pass a **JsonBasicObjectConverter**, telling it the class to return and the script location. But when serializing, you need to use a helper that defines which properties of the class to serialize. This could be done with a custom converter and its `toJson()` function. The converter would be:

```vbscript linenums="1"
Class PetConverter as AbstractJsonConverter

    Function toJson(source as Variant) as Variant
        Dim helper as New JsonConversionHelper()
        Dim jsonObj as JsonObject
        Set jsonObj = helper.withScalarConverter("name")._
            withScalarConverter("type")._
            toJson(source)
        Set toJson = jsonObj
    End Function

End Class
```

The `source` object passed into the `toJson()` function at line 3 will be the Pet object. This creates a `JsonConversionHelper`, telling it to convert "name" and "type" as scalars (lines 6 and 7), and calls the helper's own `toJson()` function on line 8 to return the Pet as a JsonObject. The resulting JsonObject is just returned as the output for the function.

This can be used with the following code:

```vbscript linenums="1"
Dim helper as New JsonConversionHelper
Dim petConverter as New PetConverter
Call helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withCustomConverter("pet", petConverter)
    
Dim jsonObj as JsonObject
Set jsonObj = helper.toJson(person)
```

A `JsonConversionHelper` is created to serialize the Person on line 1. One line 2 an instance of the `PetConverter` class is created. And the helper is loaded with `JsonScalarConverters` for "firstName" and "lastName" and the `PetConverter` for "pet" on lines 3 to 5. Then the person is serialized into a JsonObject on line 8.

## Using a JsonBasicObjectConverter

However, it's possible to serialize the Pet using a `JsonBasicObjectConverter`. But, just as you did for the custom converter, it needs to be loaded with its own `JsonConversionHelper` with information of which properties to serialize.

```vbscript linenums="1"
Dim petHelper as New JsonConversionHelper
Dim petConverter as New JsonBasicObjectConverter("Pet", "ser-30")
Dim helper as New JsonConversionHelper
Dim jsonObj as JsonObject

Set jsonObj = helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withCustomConverter("pet", petConverter._
        withHelper(petHelper._
            withScalarConverter("name")._
            withScalarConverter("type")))._
    .toJson(person)
```

First, you instantiate the variables:

- A `JsonConversionHelper` is created to serialize the pet on line 1.
- A `JsonBasicObjectConverter` is created to convert the Pet on line 2.
- A `JsonConversionHelper` is created to process everything on line 3.
- A `JsonObject` is declared for the result on line 4.

Then you're ready to get your JsonObject. The main helper is loaded with `JsonScalarConverters` for "firstName" (line 6) and "lastName" (line 7) and the `JsonBasicObjectConverter`, `petConverter` (line 8). On line 9 the `petConverter` is loaded with its own helper, `petHelper`, which needs different converters to the main helper. The `petHelper` is loaded with two `JsonScalarConverters` for "name" (line 10) and "type" (line 11). And Finally, the main helper's `toJson()` function is called on line 12, passing the Person you wish to deserialize.

## Manual conversion

If the Person object holds an array of Pets instead of just one Pet, you can just use the **JsonBasicObjectConverter**. The code is similar to the code for serializing a single Pet.

```vbscript linenums="1"
Dim petHelper as New JsonConversionHelper
Dim petConverter as New JsonBasicObjectArrayConverter("Pet", "ser-30")
Dim helper as New JsonConversionHelper
Dim jsonObj as JsonObject

Set jsonObj = helper.withScalarConverter("firstName")._
    withScalarConverter("lastName")._
    withCustomConverter("pets", petConverter._
        withHelper(petHelper._
            withScalarConverter("name")._
            withScalarConverter("type")))._
    toJson(person)
```

The only differences are that on line 2, you create `petConverter` as a `JsonBasicObjectArrayConverter` instead of a `JsonBasicObjectConverter`, and you assign the converter to the "pets" property and label on line 8.

[Example code](../../assets/example_code/ser-30.txt){: target="_new" rel="noopener noreferrer"}
