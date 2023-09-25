---
hide:
    - navigation
---
# Welcome to VoltScript JSON Converter documentation

VoltScript JSON Converter is a VoltScript library that provides classes for configured deserialization / serialization of JSON. To get a general idea, see the [simple deserialization / serialization examples](references/example.md).

---
## What's new

For the latest release information about VoltScript JSON Converter, see [What's new](references/whatsnew.md).

---
## Using via dependency management

For using with dependency management, see [Using dependency management](howto/archipelago.md)

---
## How the documentation is organized

The documentation is based on the [Diátaxis framework](https://diataxis.fr/){: target="_blank" rel="noopener noreferrer”}, which organizes documentation into the following modes to address users' documentation needs at different times and in different circumstances. Below shows an overview that guides you on where to look for needed information:

**[Tutorials](tutorial/index.md)** - Hands-on introduction on how to use VoltScript JSON Converter

**[How-to guides](howto/index.md)** - Practical step-by-step guides for performing tasks and operation

**[Topic guides](topicguides/index.md)** - High-level discussion and explanation of key topics and concepts in VoltScript JSON Converter

**[References](references/index.md)** - Contain API documentation and test reports

<!---
## Simple Deserialization Example

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

For more detailed instructions on deserialization, including using custom converters and constructors, see the [Deserialization](howto/deserialization/index.md).

## Simple Serialization Example

Using the same Person class, an object could be serialized using the following VoltScript code:

```vb
Dim helper as New JsonConversionHelper()
Dim jsonObj as JsonObject

Call helper.withScalarConverter("firstName").withScalarConverter("lastName").withScalarConverter("age")
Set jsonObj = helper.toJson(jd)
```

For more detailed instructions on serialization, including using custom converters and constructors, see the [Serialization](howto/serialization/index.md).
-->
