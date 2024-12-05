# Get Verbose Logging

## Verbose Logging

Verbose logging can be enabled by calling `JsonConversionHelper.enableDebug()` before running the conversion. This will create `LogEntry` objects for each message. However, because this goes through `DebugPrint_Samsara` function, the line number will be within that function. Use `{{CALLEELINENUM}}` to get the actual line number.

!!! important
    `{{CALLEELINENUM}}` is only available in VoltScript Logging 1.0.1.

If you are re-using the same `JsonConversionHelper` multiple times and want to reset it, you can do so by calling `JsonConversionHelper.disableDebug()`.

## Adding debugging to Custom Converters

To capture if VoltScript JSON Converter is correctly calling a function you've added, you can use `Call samsaraTouchFunction("myFunctionName")`.

To log custom messages from the converter, use `DebugPrint_Samsara getMeTypeForDebug() & "My custom message"`.