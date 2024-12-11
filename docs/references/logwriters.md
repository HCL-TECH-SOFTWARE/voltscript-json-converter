# Loading LogWriters from JSON

--8<-- "logWriters"

## Detailed description

The detailed description of the schema for each LogWriter is:

|Property|Required|Description|
|:-------||:-----:|:----------|
|writerClass|&check;|VoltScript class to use for the LogWriter|
|writerFilePath|&check;|the path relative to the project for finding the LogWriter class|
|constructorArgs|&check;|an **object** of key/value pairs mapping to arguments in the constructor|
|constructorOrder|&check;|an **array** of `constructorArgs` keys defining the order they should be passed to the constructor of the `writerClass`|

!!! tip
    A JSON object is [an unordered set of name/value pairs](https://www.json.org/json-en.html), so the `constructorOrder` property is used to enforce a consistent ordering when passing to the VoltScript class constructor.

## Validation

If you're writing the LogWriter configuration file in Visual Studio Code, the "VoltScript Build Manager" extension will validate the JSON as you enter it.

In addition, the `loadLogWritersFromJson()` function will validate each LogWriter JSON object before loading it. You can review the tests it uses by looking at the private function `validateWriterJson()` in VoltScriptJsonConverter.vss.