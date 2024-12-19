# FAQs

## My JSON is an array of objects. How do I process that?

If you're processing a string of JSON, VoltScript JSON Converter's `fromJsonString()` function automatically checks whether the top level is an array and processes it accordingly. Just remember that the response must be put into a Variant because an object array can't be assigned - the array must be iterated and each element set from a corresponding array.

If you're starting from a JsonObject of type array, use `JsonConversionHelper.jsonArrayToObjects(JsonObject, classNameForEach, libNameForClass)`.

## When I use VoltScript JSON Converter to process an array, how do I call a function to save each object to a database?

The framework is designed to convert to VoltScript objects, not to process those objects. You should convert the array of JSON objects, then iterate them in post-processing to perform your save.

Alternatively, you can parse the JSON array and use a For loop to iterate its children. Then use VoltScript JSON Converter to convert each child JSON object to a VoltScript object and call your function, before moving onto the next child.

## How do I pass the parent object as a property of a child?

VoltScript JSON Converter is designed to convert JSON objects using the hierarchy of JSON passed in. Therefore children can be properties of a parent, but not vice versa. If you wish a different class hierarchy to the JSON hierarchy, there are some options:

- Parse both as normal. Then, from the parent, create a reverse linkage back to the child.
- Parse and return the parent object, skipping the property that contains its child / children. Then use a separate helper to parse and return the child. Finally pass the parent to the child's property.
- If the child requires the parent as a parameter of the constructor, parse and return the parent object, skipping the property that contains its child / children. Then create the constructor, passing the relevant parameters, including a literal parameter containing the parent. Now use a separate helper to parse and return the child.

## The JSON I receive may not be valid

Best practice is to use VoltScript Testing Framework to perform unit tests on the JSON and validate it before passing to VoltScript JSON Converter. You can see that paradigm in practice in `loadLogWritersFromJson()`, which calls `validateWriterJson()` to validate each LogWriter JSON object. The same approach is also done in VoltScript's dependency management, in `archipelago_functions.vss`.