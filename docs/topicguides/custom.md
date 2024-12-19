# Custom converters and constructors

## Pre-packaged custom converters

The following custom converters are available out-of-the-box:

- **JsonScalarConverter** is automatically used by the JsonConversionHelper to convert strings or numbers.
- **JsonArrayConverter** is automatically used by the JsonConversionHelper to convert arrays. This can contain its own converter, to define how to serialize each element of the array.
- **JsonBasicObjectConverter** is used to convert a nested JSON object. The constructor takes two parameters, `className` for the object type to return and `libName` for the script file that contains the class. It can take its own JsonConversionHelper to define how to convert elements in the JSON object.
- **JsonBasicObjectArrayConverter** is used to convert an array of values that are JSON objects. It works the same as the JsonBasicObjectConverter.
- **JsonSetterConverter** is used to call a custom setter function in the relevant object while it's being converted from JSON. This can only be used for deserialization. The setter function will require parameters, so three function are available to pass parameters from different sources:
    - **withValueParam** is used to pass the scalar value in the JSON element with which the converter is associated.
    - **withSiblingParam** is used to pass the scalar value of a JSON element at the same level as the one with which the converter is associated. A default value is required and this will be used if the JSON element doesn't exist. 

        !!!note
            If the JSON element exists its value will be passed, even if it's an empty string or empty array. A custom converter will be used, if it matches.
          
    - **withLiteralParam** is used to pass a literal value.

- **JsonGetterConverter** is used to call a custom function in the relevant object to return a JSON value. This can only be used for serialization. The return value will be inserted, without additional conversion, into the JSON object. As a result, it can only be used if the function returns a scalar value (string, number, string of type date etc). The getter may require additional parameters. If so, two functions are available to pass parameters from different sources:
    - **withPropertyParam** is used to pass a value from the object with which the converter is associated.
    - **withLiteralParam** is used to pass a literal value.

## Custom converters and constructors

There will be scenarios where custom converters and constructors need to be written. The framework supports this. For more details, see the [How-to](../howto/index.md) section.

--8<-- "validate.md"