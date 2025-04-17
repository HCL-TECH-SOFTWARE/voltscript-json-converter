# Parse Nobel Laureates

!!! note
    The file is quite large, so running the script takes some time. You can remove some data to improve performance.

## Setup the project

1. Ensure your atlas-settings.json is set up with authentication for Volt MX Marketplace and github.com.
1. Create a folder for the project.
1. Create an atlas.json and complete mandatory elements. Set `sourceDir` to **"src"**, `libsDir` to **""libs"** and `vsesDir` to **"vses"**.
1. In the **repositories** element, add the following repository:

    ```vbscript
    {
            "id": "hcl-github",
            "type": "github",
            "url": "https://api.github.com/repos/HCL-TECH-SOFTWARE"
        }
    ```

1. In the dependencies element, add the following dependency:

    ```vbscript
            {
            "library": "voltscript-testing",
            "version": "latest",
            "module": "VoltScriptTesting.vss",
            "repository": "hcl-github"
        }
    ```

1. Save the atlas.json and ensure no validation errors.
1. Run dependency management (Ctrl + Chift + P / Cmd + Shift + P and choose "VoltScript: Install Dependencies").
1. Ensure **libs** contains "VoltScriptJsonConverter" and **vses** contains the JSONVSE extensions.

## Download the file

1. Access the JSON data from [https://api.nobelprize.org/v1/prize.json](https://api.nobelprize.org/v1/prize.json).
1. Download the data to a file called `nobel.json` in the **src** directory.

## Create the Classes

### Script Setup

1. Create a VoltScript file in **src** directory called `NobelPrizes.vss`.
1. Add `Option Declare` and `Option Public`.
1. Add a USE statement to point to your VotScriptJsonConverter.vss library. If you're using this doc repository, then `Use "../libs/VoltScriptJsonConverter"` should work.

### Prize Class

1. Create a class called "Prize".
1. Add a public String variable called "year" (`Public year as String`).
1. Add a public String variable called "category".
1. Add a public String variable called "overallMotivation.
1. Add a public Variant variable called "laureates".

!!! warning
    All classes need to be public for VoltScript JSON Converter to create an instance. This is because it will run an `Execute` statement pointing to this script. If the class is private, the `Execute` statement will fail.

### Laureate Class

1. Create a class called "Laureate"
1. Add a public String variable called "id".
1. Add a public String variable called "firstName".
1. Add a public String variable called "surname".
1. Add a public String variable called "motivation".
1. Add a public String variable called "share".
1. Add a `getName()` function that returns a String. Return `Me.firstName & " " & Me.surname`.

## Loading the JSON file

1. Create a `Sub Initialize`. This will be triggered automatically by VoltScript when the script runs. Ensure the name is correct, or the code won't be triggered.
1. Add the following code:

    ```vbscript
    Dim parser as new JsonParser()
    Dim root as JsonObject
    Dim prizes as Variant

    Call parser.loadFromFile(CurDir() & "/src/nobel.json")
    Set root = parser.getRootObject
    prizes = root.getChild("prizes").getChildren()
    ```

    This loads the file and gets the "prizes" JSON object.

!!! warning
    **CurDir()** returns a RUN-TIME representation of the directory from which the script is BEING RUN; not the COMPILE-TIME directory in which that the script file resides. For Visual Studio Code, this is the folder that is open.

!!! note
    You can test the code by adding `Print root.getChild("prizes").shortValue()`, to verify it's loaded the file correctly. You can also use a Try...Catch...End Try block to Catch errors.

## Converting the JSON

1. Add the following code:

    ```vbscript
    Dim helper as New JsonConversionHelper
    Dim converter as New JsonBasicObjectArrayConverter("Laureate", "NobelPrizes")
    Dim prizeObj as JsonObject
    Dim prize as Prize
    Dim i as Integer
    Dim prizeList List as Prize

    For i = 0 to UBound(prizes)
        Set prizeObj = prizes(i)
        Set prize = helper.withCustomConverter("laureates", converter)._
            toObject(prizeObj, "Prize", "NobelPrizes")._
            fromJson(prizeObj)
        Set prizeList(prize.year & "-" & prize.category) = prize
    Next
    ```

1. You can then check information based on properties of the VoltScript objects. For example, `Print prizeList("2021-economics").laureates(0).getName()` will print the name of the first Laureate for the 2021 Economics prize.

??? success
    The name for the first Laureate for the 2021 Economics Nobel Prize was **David Card**.

!!! example "Challenge Yourself"

    For more advanced VoltScript, Try:

    - Iterating and only loading prizes for 2021, exiting after 2021 is completed.
    - Navigating directly to the JSON object for prizes for 2021 and only loading those prizes.
    - Manually parsing the JSON using JsonLSX to get the same information.