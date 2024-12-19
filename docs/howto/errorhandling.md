# Error handling

## Using VoltScript Logging

VoltScript JSON Converter makes use of VoltScript Logging, which provides the ability to track and log error information.

!!! important
    To retrieve output, you must add at least one LogWriter to the `globalLogSession` and ensure the code does not abort with an uncaught error, or logs do not get written.

## VoltScript JSON Converter behavior

The intention when adding error handling to VoltScript JSON Converter has been to optimize the development experience as much as possible, so that when bad data (inevitably) is submitted, you can fix the data and process successfully as quickly as possible.

- To process as much of the JSON as possible before returning an error.
- To notify you of as many problems with the JSON.
- If a constructor fails, obviously we cannot check the serialization of properties.
- If `suppressErrors` is set to True, errors with setting properties are not logged. If errors occur, the property will remain as the default value for that datatype ("" for a string, 0 for a number, False for a boolean etc).
- When `suppressErrors` is set to True, if a constructor fails, an error will still be thrown.

!!! note
    When building code for a constructor, the scalar values will be extracted from the JSON object. But there is no way to know what datatype the code for the constructor expects. This can result in a JSON object containing a String for a property, but the constructor expects an Integer, causing the constructor code to fail with no way to successfully create the relevant Object.

### JSON arrays, suppressErrors and constructor errors

But remember that first point, to process as much of the JSON as possible. That changes how an error thrown from invalid constructor code should be handled when converting JSON objects to VoltScript objects.

``` mermaid
flowchart TD
A([Start]) --> B(Process JSON array)
B --> C(Process JSON Object)
C --> D(Create VoltScript object)
D --> E{Error encountered}
E -- No --> F(Deserialize properties)
F --> G{More objects?}
G -- Yes --> C
E -- Yes --> H(Throw error)
H --> J(Create custom ErrorEntry<BR/>logging array index)
J --> K(Set Variant index to Nothing)
K --> G
G -- No --> L{Is suppressErrors False}
L -- No --> M([Return Variant array])
L -- Yes --> N{Were there errors?}
N -- No --> M
N -- Yes --> O([Throw error])
```

You can find all detailed errors for individual array elements by querying the ErrorSession or checking the logs, if you have added a LogWriter to the LogSession.

--8<-- "validate.md"