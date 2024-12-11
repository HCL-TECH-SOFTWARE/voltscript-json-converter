# Use and extend JsonSetterConverters

## Using the JsonSetterConverter

Imagine you have the following JSON `[{"firstname": "Guglielmo", "surname": "Marconi", "share": "2"},{"firstname": "Ferdinand", "surname": "Braun", "share": "2"}]` to pass into the following class:

``` vbscript
Class Laureate
    Public name as String
    Public share as String

    Sub setName(firstName as String, lastName as String)
        Me.name = firstName & " " & lastName
    End Sub
End Class
```

The `firstname` and `surname` elements in the JSON object need passing to the `setName()` sub in the class. You need to use a **JsonSetterConverter** for the name. This can be done with the following code.

``` vbscript
Dim setterConverter as New JsonSetterConverter()
Call setterConverter.forSetter("setName")._
    withValueParam()._
    withSiblingParam("surname", "")
```

First, you instantiate the JsonSetterConverter. On the next line, you map it to the `setName()` function. Then you need to tell it how to find the parameters. The first parameter is the "firstname" element in the JSON. You will bind the JsonSetterConverter to this JSON element, so you need to pass the element's value with `withValueParam()`. The second parameter is the "surname" element adjacent to `firstname`, so you use `withSiblingParam()` to pull in that element's value, with a default value of `""`.

You then need to pass the converter to the helper.

``` vbscript
Dim helper as new JsonConversionHelper()
Call helper.withCustomConverter("firstname", setterConverter)
```

You also need to ignore the "surname" label, because you are handling it in the jsonSetterConverter. This can be done in two ways, calling `helper.ignoreLabel("surname")` or suppressing errors with `helper.suppressErrors = True`.

Finally, run the conversion.

``` vbscript
Dim laureates as Variant
laureates = helper.fromJsonString(json, "Laureate", "deser-30")
```

[Example code](../../assets/example_code/deser-30.txt){: target="_new" rel="noopener noreferrer"}

## Creating a custom JsonSetterConverter

Imagine you have the following JSON `{"enable":1, "timestamp": 1564741004, "meterLocation": 0}` to pass into the following class:

``` vbscript
Class HomeData
    Public timestamp as Double
    Private enabled as Boolean
    Public meterLocation as Integer

    Sub enable()
        Me.enabled = True
    End Sub

    Sub disable()
        Me.enabled = False
    End Sub

    Function isEnabled() as Boolean
        isEnabled = Me.enabled
    End Function
End Class
```

The enabled property in the class is private, so you can't just pass it into the object. Instead, call either `HomeData.enable()` or `HomeData.disable()`. You need to use a custom **JsonSetterConverter** to define the method to call, depending on the `enabled` property. This can be done with the following code.

``` vbscript
Class HomeDataSetterConverter as JsonSetterConverter
    Sub loadParamValuesFromJsonObject(source as JsonObject, converters List as AbstractJsonConverter)
        If (source.scalarValue = 1) Then
            Me.forSetter("enable")
        Else
            Me.forSetter("disable")
        End If
    End Sub
End Class
```

You extend the `loadParamValuesFromJsonObject()` function using the same signature. You are processing the "enabled" property of the JSON object, so in line 3, you check the scalar value. If it's `1`, you want to run the setter `enable()` so you pass that setter name to the `forSetter()` function. Otherwise, run `disable()` so you pass that setter name to the `forSetter()` function.

You then need to pass the converter to the helper.

``` vbscript
Dim dataConverter as new HomeDataSetterConverter
Call helper.withCustomConverter("enable", dataConverter.forSetter("enable"))
```

In line 2, you need to pass _something_ as a default argument for `dataConverter.forSetter`, even though the code overrides it, otherwise the `deserialize()` function will error. So you pass "enable" as the default.

[Example code](../../assets/example_code/deser-40.txt){: target="_new" rel="noopener noreferrer"}