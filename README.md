# VoltScript JSON Converter

VoltScript JSON Converter manages the conversion of data from JSON to VoltScript objects and back to JSON.

## How it Works

As a compiled language, VoltScript cannot leverage reflection to convert the data. However, the `Execute` function in VoltScript can be leveraged with global variables. A string can be built for the code that needs to be run, and `Execute` can perform that code.

This just leaves a few complications:

- Classes that require a custom constructor.
- Custom converters to convert to / from specific data types
- Custom converters that require different property names to JSON labels
- Custom converters that need to call subs or functions instead of writing directly to properties

## Using dependency management

Dependency management is available in the documentation for each project, but also aggregated here:

### Authentication

You'll need a [Personal Access Token](https://help.hcltechsw.com/docs/voltscript/early-access/howto/writing/archipelago.md#github-personal-access-token) to use GitHub REST APIs. You'll then need to add this to the JSON object in your [atlas-settings.json](https://help.hcltechsw.com/docs/voltscript/early-access/howto/writing/archipelago.md#atlas-settingsjson), in the .vss directory of your user home directory:

```json
    "hcl-github": {
        "type": "github",
        "token": "${env.TOKEN}"
    }
```

For JsonVSE downstream dependency, you'll need to sign up for the Volt MX Demo Marketplace. Instructions are in the [how-to-guide](https://help.hcltechsw.com/docs/voltscript/early-access/howto/writing/archipelago.html#volt-mx-marketplace-credentials). **NOTE:** Ensure you log into the Volt MX Marketplace via a browser after confirming your account, to ensure the account is properly activated. Otherwise authentication to gain an access token will fail.

You'll then need to add this to the JSON object in your [atlas-settings.json](https://help.hcltechsw.com/docs/voltscript/early-access/howto/writing/archipelago.md#atlas-settingsjson), in the .vss directory of your user home directory:

```json
    "volt-mx-marketplace": {
        "type": "marketplace",
        "username": "YOUR_USERNAME",
        "password": "YOUR_PASSWORD",
        "authUrl": "https://accounts.auth.demo-hclvoltmx.net/login"
    }
```

### Repository

You'll need to add to your **repositories** object in the atlas.json of your project:

```json
        {
            "id": "hcl-github",
            "type": "github",
            "url": "https://api.github.com/repos/HCL-TECH-SOFTWARE"
        }
```

### Dependency

You'll need the relevant dependency to add to your **dependencies** or **testDependencies** object in the atlas.json of your project:

```json
        {
            "library": "voltscript-json-converter",
            "version": "1.0.0",
            "module": "VoltScriptJsonConverter.vss",
            "repository": "hcl-github"
        }
```

## Contributing

See [CONTRIBUTING.md](contributing.md).

## Code of Conduct

See [CODE_OF_CONDUCT.md](code_of_conduct.md).

## Issues and discussions

Let's chat on [OpenNTF Discord](https://openntf.org/discord).

For long-running discussions, use Discussions area in GitHub. For bugs and feature requests **specific to VoltScript Testing Framework** use, Issues area.
