# Use the JsonCustomConstructor

## Simple example

Imagine the following class:

``` vbscript
Class Session

    Public id as String
    Public title as String

    Sub New(id as String, title as String)
        Me.id = id
        Me.title = title
    End Sub

End Class
```

The constructor takes parameters that will be in the JSON object being parsed. So a custom constructor is needed.

Like the JsonSetterConverter, the **JsonCustomConstructor** allows you to define how to extract parameters. Out of the box, parameters can be literals or can be extracted from the current JSON object.

``` vbscript
Dim sess as Session
Dim helper as New JsonConversionHelper
Dim sessionConstructor as new JsonCustomConstructor
Dim json as String

json = |{"id": "Ad01","title": "Domino HA"}|
Call sessionConstructor.withParam("id","").withParam("title", "")
Set sess = helper.withCustomConstructor(sessionConstructor).fromJsonString(json, "Session", "deser-70")
```

You create a new JsonCustomConstructor and define where to find the two parameters - id and title - in that order. By adding the custom constructor to the helper, it will be picked up by the `toObject()` function of the JsonConversionHelper for creating the Session.

[Example code](../../assets/example_code/deser-70.txt){: target="_new" rel="noopener noreferrer"}

## Using a JsonCustomConstructor inside a custom converter

Imagine the following classes:

``` vbscript
Class Person

    Public firstName as String
    Public lastName as String
    Public pet as Pet

End Class

Class Pet

    Public name as String
    Public type as String

    Sub New(petName as String, petType as String)
        Me.name = petName
        Me.type = petType
    End Sub

End Class
```

You need a custom converter for the "pet" property, but that needs to call a custom constructor. You can do this with the following converter.

``` vbscript linenums="1"
Class PetConverter as AbstractJsonConverter

    Function fromJson(source as JsonObject) as Variant

        Dim pet as Pet
        Dim helper as New JsonConversionHelper()
        Dim petConstructor as New JsonCustomConstructor()
        Call petConstructor.withParam("name", "").withParam("type", "")
        Set pet = helper.withCustomConstructor(petConstructor)._
            toObject(source, "Pet", "deser-80")._
            fromJson(source)
        Set fromJson = pet

    End Function

End Class
```

On line 5, you declare a Pet object to hold the converted object and pass back from the `fromJson()` function. On line 6, you create a new `JsonConversionHelper` to perform the conversion. Then on lines 7 and 8, you create a `JsonCustomConstructor` to create the Pet object, and tell it to look for "name" as the first parameter and "type" as the second.

On line 9, you call the helper, pass the petConstructor, and tell it to create an object of type "Pet" from "ComplexPersonConstructorTest" library from the source JSON. The source will just be the value of the "pet" element.

Now you're ready to use the converter.

``` vbscript linenums="1"
Dim helper as new JsonConversionHelper()
Dim petConverter as New PetConverter()
Dim person as Person
Dim json as String
    
json = |{"firstName":"Ron","lastName":"Burgundy","pet": {"name":"Baxter","type":"Dog"}}|
Call helper.withCustomConverter("pet", petConverter)
Set person = helper.fromJsonString(json, "Person", "deser-80")
```

Create the petConverter and helper, pass the converter into the helper on line 7, and load it from the JSON string.

[Example code](../../assets/example_code/deser-80.txt){: target="_new" rel="noopener noreferrer"}
