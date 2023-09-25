# Create a custom converter

## Overriding toJson()

Imagine you have a class that has `Public modified as Variant` where this is a date variant. But you want to output it in JSON as a string of date-time format. This can't be directly serialized to a JsonObject.

First, create your custom converter class:

```vbscript
Class DateTimeSerializer as AbstractJsonConverter

End Class
```

For the body of the `toJson()` function, get the string value from the "modified" element and convert it to an ISO date string. So the complete function is:

```vbscript
Function toJson(source As Variant) As Variant
	toJson = Format(source, "yyyy-mm-ddThh:nn:ssZ")
End Function
```

You can use it with the following code:

```vbscript
Dim helper as new JsonConversionHelper
Dim parser as New DateTimeParser
Dim dateConverter as New DateTimeSerializer
Dim obj as New ObjectSummary
Dim jsonObj as JsonObject

obj.unid = "12345678901234567890123456789012"
obj.action = "created"
Set obj.modified = DateNumber(2022,2,2) + TimeNumber(2,2,22)
Call helper.withCustomConverter("modified", dateConverter)._
    withScalarConverter("action")._
    withScalarConverter("unid")
Set jsonObj = helper.toJson(obj)
```

## Overriding serialize()

Occasionally, just converting the value won't be enough. You might need to do something more extensive with the value you're deserializing. Imagine the JSON should only include a "modified" element if it's been set.

First, create your custom converter class:

```vbscript
Class DateTimeOrNothingSerializer as AbstractJsonConverter

End Class
```

You need to override the standard `serialize()` sub, which needs the same signature as the sub in the base class, so `Sub serialize(source As Variant, target as JsonObject)`. The `labelName` parameter will be the label to write to in the JsonObject. `source` will be the current object, of type ObjectSummary. `target` will be the JsonObject you're writing to. The code will be:

```vbscript
Sub serialize(source As Variant, target as JsonObject)
    If (Not IsEmpty(source.modified)) Then
        Call target.insertValue(Me.labelName, Format(source.modified, "yyyy-mm-ddThh:nn:ssZ"))
    End If
End Sub
```

If `source.modified` isn't set, do nothing. Otherwise, convert the value to an ISO date-time string and insert into the JsonObject. If modified is set, you will get:

```json
{
  "action": "created",
  "modified": "2022-02-02T02:02:22Z",
  "unid": "12345678901234567890123456789012"
}
```

If not, you will get:

```json
{
  "action": "created",
  "unid": "12345678901234567890123456789012"
}
```

[Example code](../../assets/example_code/ser-40.txt){: target="_new" rel="noopener noreferrer"}
