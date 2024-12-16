# Loading LogWriters from JSON

--8<-- "logWriters"

## Loading LogWriters

To load LogWriters from in-memory, the best practice approach would be:

1. Build a JSON object that conforms to the schema above, using JsonVSE.
1. Call `toString(False)`method to convert it to a JSON string.
1. Pass the string to `loadLogWritersFromJson()`.

To load from a file:

1. Get the filepath as a string relative to the project directory.
1. Pass the string to `loadLogWritersFromJson()`.

The LogWriters created will automatically be added to the `globalLogSession`. The function will also return a Variant array of LogWriter objects, if you wish to do additional processing, for example removing specific LogWriters based on particular business logic.

## Writing LogWriter JSON files

The "VoltScript Build Manager" Visual Studio Code extension will validate JSON against the schema, providing the filename ends "vslogging.conf". There is also a snippet available with the snippet prefix "vslogging". This is the recommended approach.

All property name in `constructorArgs` should also exist in `constructorOrder`. However, a JSON schema cannot validate this. So if you omit one or type its name incorrectly, you will not be warned when saving the file.