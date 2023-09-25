# Use JsonGetterConverters

## Simple JsonGetterConverter

The previous examples only address scenarios where the properties in your class map directly to the entities in the JSON you want to output. That's not always the case. You could manually generate the JSON using a `toJson()` function in your class. Alternatively, if you class has a function that outputs what you need, you can use a **JsonGetterConverter**. Imagine you have the following class:

```vbscript
Class Laureate
    Public firstName as String
    Public lastName as String
    Public share as String

    Function getName() as String
        getName = firstName & " " & lastName
    End Function
End Class
```

Use a **JsonGetterConverter** to output a name entity. This can be done with the following code.

```vbscript linenums="1"
Dim helper as New JsonConversionHelper
Dim jsonObj as JsonObject
Dim nameConverter as New JsonGetterConverter()
Set jsonObj = helper.withScalarConverter("share")._
    withCustomConverter("name", nameConverter.forGetter("getName"))._
    toJson(laureate)
```

You instantiate the `JsonGetterConverter` on line 3. You convert the JsonObject, using a `JsonScalarConverter` for "share" in line 4, and passing the `JsonGetterConverter` as a custom converter writing to the "name" element and for the getter `getName` in line 5.

## Passing parameters to getters

With the custom setters, you had a variety of parameter types that could be passed - the current value, a sibling value or a literal value. With custom getters there is only one parameter type - a literal value. The expectation is that a getter that requires another value from the current VoltScript object should be accessing it directly rather than receiving it as a parameter.

Imagine you have the following class:

```vbscript
Class Laureate
    Public firstName as String
    Public lastName as String
    Public share as String

    Function getFullName(firstNameFirst as Boolean)
        If (firstNameFirst) Then
            getFullName = Me.firstName & " " & Me.lastName
        Else
            getFullName = Me.lastName & ", " & Me.firstName
        End If
    End Function
End Class
```

You need to use a JsonGetterConverter to output the full name, passing a parameter. This can be done with the following code:

```vbscript
    Dim helper as New JsonConversionHelper
    Dim jsonObj as JsonObject
    Dim nameConverter as New JsonGetterConverter()
    Set jsonObj = helper.withScalarConverter("share")._
        withCustomConverter("name", nameConverter._
            forGetter("getFullName")._
            withLiteralParam(true))._
        toJson(laureate)
```

[Example code](../../assets/example_code/ser-50.txt){: target="_new" rel="noopener noreferrer"}
