VoltScript JSON Converter provides the ability to load LogWriters from a JSON string or a JSON file. The function takes a single argument for the context from which to load the LogWriters.

- If the value passed can be parsed as JSON, that will be used.
- If not, it will be treated as a filepath that contains JSON.

## Sample structure

!!! note
    Required properties are highlighted

The JSON should be an array of log writers, each of which will be added to the `globalLogSession`.

![logWriterJson](../assets/images/plantuml/logwriters.png)

`constructorArgs` are the arguments required by the constructor. This will vary from class to class - there may be fewer or there may be more. But all argument names should also be included in `constructorOrder` to ensure they are passed to the class constructor in the right position.