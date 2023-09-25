# Aims for the framework

- Minimize the code required to consume JSON as much as possible.
- Avoid the need for non-standard quirks in VoltScript Classes.
- Encourage the use of VoltScript Classes when integrating with REST services.
- Provide intuitive coding structures.
- Provide a pleasurable coding experience.

Like frameworks in other languages, the framework is designed _only_ to speed up conversion from JSON to VoltScript objects, so that they can subsequently be processed _as_ VoltScript objects. The framework isn't designed to enable you to complete all your custom processing directly from the JSON objects. To do so would overcomplicate the framework.

The framework can be used to create relationships, but only uni-directionally, like in the corresponding JSON. The framework can use JSON in the format `{"firstName":"Ron","lastName":"Burgundy","pet": {"name":"Baxter","type":"Dog"}}` to define a `pet` property in a Person object, but not an `owner` property in the Pet object. That should be done as part of your subsequent processing.