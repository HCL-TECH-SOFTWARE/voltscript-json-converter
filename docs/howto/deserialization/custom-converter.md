# Create a custom converter

## Overriding fromJson()

Imagine you have a JSON object that contains an element `"modified":"2022-02-02T02:02:22Z"`, but your class has `Public modifiedDate as Variant`.

First, create your custom converter class:

``` vbscript
Class DateTimeSerializer as AbstractJsonConverter

End Class
```

For the body of the `fromJson()` function, get the string value from the "modified" element and convert it to a DateTimeObject. So the complete function is:

``` vbscript
Function fromJson(source As JsonObject) As Variant
    Dim dt as String
    Dim dateVal as String
    Dim timeVal as String
    Dim dateElems as Variant
    Dim timeElems as Variant

    dt = source.scalarValue
    dateVal = StrLeft(dt, "T")
    dateElems = Split(dateVal, "-")
    timeVal = Left$(StrRight(dt, "T"), 8)
    timeElems = Split(timeVal, ":")
    Return DateNumber(dateElems(0), dateElems(1), dateElems(2)) + TimeNumber(timeElems(0), timeElems(1), timeElems(2))
End Function
```

You can use it with the following code:

``` vbscript
Dim helper as New JsonConversionHelper
Dim dateConverter as New DateTimeSerializer()
Call dateConverter.forPropertyName("modifiedDate")   'Redirect value to modifiedDate property of our class
Call helper.withCustomConverter("modified", dateConverter)
Set obj = helper.fromJsonString(json, "ObjectSummary", "deser-50")
```

This creates an instance of DateTimeSerializer, maps it to the `modifiedDate` property of the class and maps it from the `modified` element of the json.

!!! warning
    Date variants are not intended to be timezone-specific. You should consider using ZuluVSE instead.

## Overriding deserialize()

Occasionally, just converting the value won't be enough. You might need to do something more extensive with the value you're deserializing. Imagine you have a JSON object that has an element `"startTime":"2022-06-03T10:10:30Z"`, but the class you're writing to has `Public startDate as Variant` and `Public startTime as Variant`.

You need to take a single string coming in and split it across two Date variants, one for the date and one for the time. The standard converters aren't set up for that. `fromJson()` will return a single value to write to a single field.

First, create your custom converter class:

``` vbscript
Class ComplexDateTimeSerializer as AbstractJsonConverter

End Class
```

Override the standard `deserialize()` sub, which needs the same signature as the sub in the base class, so `Sub deserialize(holder as JsonConversionHolder, source as JsonObject)`. The `holder` parameter is a JsonConversionHolder object, which has a `contents` property containing the instance of the class you're deserializing to. Your code will be:

``` vbscript linenums="1"
Sub deserialize(holder as JsonConversionHolder, source as JsonObject)
    Dim dateTime as String
    Dim dateVal as String
    Dim timeVal as String
    Dim dateElems as Variant
    Dim execString as String

    Set samsaraObj = holder.contents

    dateTime = source.scalarValue
    dateVal = StrLeft(dt, "T")
    dateElems = Split(dateVal, "-")
    timeVal = Left$(StrRight(dt, "T"), 8)

    ' Build execution string
    Try
        execString = |samsaraObj.startDate = DateNumber(| & dateElems(0) & |,| & dateElems(1) & |,| & dateElems(2) & |)|
        execString = execString & Chr(10) & |samsaraObj.startTime = CDat("| & timeVal & |")|
        DebugPrint_Samsara getMeTypeForDebug() & "Executing " & execString
        Execute execString
        DebugPrint_Samsara getMeTypeForDebug() & "Executed " & execString
    Catch
        DebugPrint_Samsara GetThreadInfo(12)
        Dim msg as String
        msg = Chr(10) & Chr(9) & getMeTypeForDebug() & "Cannot deserialize: " & Error() & " on line " &Erl
        Error 1500, msg
    Finally
        Call Me.cleanup()
    End Try
End Sub
```

On line 8, you put the holder contents - the object you're writing to - in the global variable `samsaraObj`. 

On line 10, you extract the ISO date-time into a temporary variable `dateTime`. Then on lines 11 and 12, you extract the date portion and split it into an array with three elements - year, month, day. You also extract the time portion on line 13.

Finally on line 17, you build the string to create and write the date variants. `DateNumber()` allows us to pass year, month, and day in that order. These are expected as numbers, so you just add the variables to the string. At the point `execString` is `samsaraObj.startDate = DateNumber(2022,06,03)`.

On line 18, you use `CDat()` for the time portion. This function takes a string, so you need to add explicitly string delimiters into `execString`. This string will become `samsaraObj.startTime = CDat("10:10:30")`.

In the final block on line 28, you call `Me.cleanup()` to clear the global variables.

[Example code](../../assets/example_code/deser-50.txt){: target="_new" rel="noopener noreferrer"}
