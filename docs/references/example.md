# Deserialization / serialization examples

## Simple deserialization example

Let's assume a basic class for a Person:

```vb
Class Person
    Public firstName as String
    Public lastName as String
    Public age as Integer
End Class
```

A corresponding JSON structure would be:

```json
{"firstName":"John","lastName":"Doe","age":42}
```

This could be processed with the following VoltScript code:

```vb
Dim json as String
Dim helper as New JsonConversionHelper()
Dim jd as Person

json = |{"firstName":"John","lastName":"Doe","age":42}|
Set jd = helper.fromJsonString(json, "Person", "PersonIndex")
```

For more detailed instructions on deserialization, including using custom converters and constructors, see [Deserialization](../howto/deserialization/index.md).

## Simple serialization example

Using the same Person class, an object could be serialized using the following VoltScript code:

```vb
Dim helper as New JsonConversionHelper()
Dim jsonObj as JsonObject

Call helper.withScalarConverter("firstName").withScalarConverter("lastName").withScalarConverter("age")
Set jsonObj = helper.toJson(jd)
```

For more detailed instructions on serialization, including using custom converters and constructors, see [Serialization](../howto/serialization/index.md).