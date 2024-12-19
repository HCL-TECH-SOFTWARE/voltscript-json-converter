# What's new

The section provides information on the latest features, improvements, and resolved issues related to VoltScript JSON Converter.

<!-- prettier-ignore -->
!!! note "Important"
    - Items marked in <span style="color:red">**red**</span> are API changes that may impact your applications and should be reviewed before upgrading.

???+ info "v1.0.5 - What's new or changed"

    ## v 1.0.5
    **Improvements**

    - Add function to load LogWriters from a file.
    - Add VoltScriptLogging to VoltScript JSON Converter and documentation.
    - <span style="color:red">Changed `failSilently()` function to `suppressErrors()` boolean property, for consistency with VoltScript Collections.</span>
    - API Docs updated.
    - Documentation on using VoltScript Testing Framework to validate before conversion.

???+ info "v1.0.4 - What's new or changed"

    ## v1.0.4
    **Improvements**

    - <span style="color:red">Repointing atlas.json to use VSE title and filename as library and module.</span>

???+ info "v1.0.3 - What's new or changed"
    ## v1.0.3
    **Improvements**

    - Added VSID database to repo
    - <span style="color:red">Repointing atlas.json from demo marketplace. atlas-settings marketplace url will need updating to "https://accounts.auth.hclvoltmx.net/login"</span>

???+ info "v1.0.2 - What's new or changed"
    ## v1.0.2

    **Resolved Issues**

    - Fixed JsonArrayConverter to return JSON array instead of JSON object for empty Variants
    - Moved voltscript-testing to test dependency

???+ info "v1.0.1 - What's new or changed"
    ## v1.0.1
    **Improvements**

    - Updated API doc from VoltScript Interface Designer.
    - Code merged with skeletons auto-generated from VoltScript Interface Designer.


??? info "v1.0.0 - What's new or changed"
    ## v1.0.0

    - First release version of VoltScript JSON Converter.