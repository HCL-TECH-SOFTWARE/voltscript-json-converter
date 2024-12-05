# Error handling

## Using VoltScript Logging

VoltScript JSON Converter makes use of VoltScript Logging, which provides the ability to track and log error information.

## VoltScript JSON Converter behavior

By default, VoltScript JSON Converter classes will spawn `ErrorEntry` instances and add them to the global `ErrorSession` instance and `globalLogSession` instance.

Fatal errors (for example, when creating the object to serialize into) will be added to the global `ErrorSession` and re-thrown.

## FailSilently Behavior

The JsonConversionHelper class has a method `failSilently()` to prevent errors being thrown.
<!-- FIXME: This needs fixing -->