# Use object and object array converters

## Basic object converter

Imagine you have the following JSON `|{"firstName":"Ron","lastName":"Burgundy","pet": {"name":"Baxter","type":"Dog"}}|`.

You want to convert it into a Person object which has a Pet, where the classes are like this:

```vbscript
Class Person

    Public firstName as String
    Public lastName as String
    Public pet as Pet

    Function getName() as String
        getName = firstName & " " & lastName
    End Function

End Class

Class Pet

    Public name as String
    Public type as String

End Class
```

In this case, you don't need a custom converter and just use the **JsonBasicObjectConverter**. You can just use the following code:

``` vbscript
Dim helper as new JsonConversionHelper()
Dim petConverter as New JsonBasicObjectConverter("Pet", "deser-20")
Dim person as Person
Dim json as String

json = |{"firstName":"Ron","lastName":"Burgundy","pet": {"name":"Baxter","type":"Dog"}}|
Call helper.withCustomConverter("pet", petConverter)
Set person = helper.fromJsonString(json, "Person", "deser-20")
```

## Basic object array converter

Imagine you have the following JSON `|{"firstName":"Tom","lastName":"Popper","pets": [{"name":"Captain","type":"Gentoo Penguin"},{"name":"Loudy","type":"Gentoo Penguin"},{"name":"Nimrod","type":"Gentoo Penguin"}]}|`. In this case, the Person class contains an array of Pets. Again, you don't need a custom converter and just use the **JsonBasicObjectArrayConverter**. You can just use the following code:

``` vbscript
Dim helper as new JsonConversionHelper()
Dim petConverter as New JsonBasicObjectArrayConverter("Pet", "deser-20")
Dim person as ComplexPerson
Dim json as String

json = |{"firstName":"Tom","lastName":"Popper","pets": [{"name":"Captain","type":"Gentoo Penguin"},{"name":"Loudy","type":"Gentoo Penguin"},{"name":"Nimrod","type":"Gentoo Penguin"}]}|
Call helper.withCustomConverter("pets", petConverter)
Set person = helper.fromJsonString(json, "ComplexPerson", "../example_code/deser-20")
```

However, you need to ensure the class is set up correctly. The `pets` property needs to be declared as a Variant, because if it were declared as a string array, it cannot be assigned, only modified. So the Person class will need to look like this:

```vbscript
Class ComplexPerson

    Public firstName as String
    Public lastName as String
    Public pets as Variant

End Class
```

[Example code](../../assets/example_code/deser-20.txt){: target="_new" rel="noopener noreferrer"}
