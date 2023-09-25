# How it works

As a compiled language, VoltScript can't leverage reflection to convert the data. However, the `Execute` function in VoltScript can be leveraged with global variables. A string can be built for the code that needs to be run, and `Execute` can perform that code.

This covers the basics of converting strings, numbers, and arrays of strings or numbers. But the framework also provides functionality for more sophisticated deserialization scenarios:

- Classes that require a custom constructor.
- Custom converters to convert to / from specific data types.
- Custom converters that require different property names to JSON labels.
- Custom converters that need to call subs or functions instead of writing directly to properties.
- Ignoring specific labels in the JSON.

## Entrypoint

The JsonConversionHelper is the object to use for converting from JSON. The Helper can contain:

- A **JsonConstructor** from which to create the top-level object (if parsing a single JSON object) or objects (if parsing an array of objects).
- An array of **JsonConverters** for custom deserialization of elements from the JSON.
- An array of labels to ignore from the JSON.

For deserialization, the labels in the JSON object are iterated. But for serialization, VoltScript can't use reflection to know the properties available. So converters must be passed and mapped to properties or functions. More details are in the [How To Guides](../howto/index.md).

## Classes with fromJson / toJson function

If a class has a `fromJson()` or a `toJson()` function with the correct signature, this will be used instead of iterating elements within the JSON. More details are in the [How-to guides](../howto/index.md).