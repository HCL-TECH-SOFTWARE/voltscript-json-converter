# Parsing CollabSphere Sessions 

## Pre-Requisites 
1. Complete [Parsing Nobel Laureates](laureates.md)


## Objectives 
1. Gain experience working with VoltScript in a Visual Studio Code environment.
1. Learn about working with VoltScript Extensions (specifically JsonVSE).
1. Introduce the VoltScript Json Converter Libray 
1. Gain familiarity with processing JSON and the use of Custom Converters 



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


## Get the JSON file
1. Access the JSON data from the Collabsphere [Website](https://collabsphere.org/ug/cs2023.nsf/ajax_Calendar_GetAllSessions.json?OpenAgent&start=2023-08-29&end=2023-09-01).
1. Download the data and save it to a file called `collabsphere.json` in the **src** directory.    
!!! note
    The "live" version of this file is pulled by referencing a JSON agent running on the CollabSphere website.  
         - Access to this live data _cannot be guaranteed_.  
         - If the live version is unavailable use the included sample instead:   [collabsphere.json](https://github.com/hcl-tech-software/VoltScript-JSON-Converter/samples/collabsphere.json)


## Script Setup
1. Create a VoltScript file in **src** directory called `CollabSphere.vss`.
1. Add `Option Public` and `Option Declare`.
1. Add a USE statement to point to your VotScriptJsonConverter.vss library. If you're using this doc repository, then `Use "../libs/VoltScriptJsonConverter"` should work.


## Create the Classes
!!! warning
    All classes need to be public for VoltScript JSON Converter to create an instance. This is because it will run an `Execute` statement pointing to this script. If the class is private, the `Execute` statement will fail.


### Session Class
1. Create a class called "Session".
1. Add a public String variable called "debuglog" (`Public debuglog as String`).
1. Add the following additional public String variables: id, title, room, start, end, backgroundColor, textColor, and className.
1. Add a constructor ("New()") sub that accepts a single argument to set the object's title
1. Add a _printSummary_ method that prints the object's title, time, room, and id

??? example "constructor sub" 
    ``` vbscript
    Sub New(title As String)
        Me.title = title 
    End Sub
    ```
??? example "printSummary method" 
    ``` vbscript
    Sub printSummary()
        Print "Title:   " & Me.title 
        Print "Time:    " & Me.start & | - | & Me.end
        Print "Room:    " & Me.room  
        Print "Id:      " & Me.id
    End Sub
    ```

## Parse the JSON file
Open your VSCode IDE and edit your `CollabSphere.vss` file. 

### Load the JSON
1. Create a `Sub Initialize`. This is the default method that will be automatically invoked by the  VoltScript processor.  Ensure the name is correct, or the code will not run correctly.
1. Add the following declarations code:
    ```vbscript
    Dim job As JsonObject

    Dim parser As New JsonParser()
    Dim helper As New JsonConversionHelper()
    Dim sessionConstructor As New JsonCustomConstructor()
    Dim jscDebugLog As New JsonScalarConverter()
    Dim ogs As Session

    Dim sessions As Variant
    Dim i As Integer
    ```
    An explanation of these variables is in order. 
    - `job`: This is the root JSON Object used for parsing JSON content. 
    - `parser`: A parsing worker object. 
    - `helper`: A parsing helper object.
    - `sessionConstructor`: This is a customer constructor object, it will allow us to modify constructor argument values. 
    - `jscDebugLog`: This object is used to convert json scalar values by name.
    - `ogs`: A working object instance of the previously defined Session class.
    - `sessions`: This is a container for carrying multiple instances of our Session class 
    - `i`: Nothing more than a simple index counter
1. Add the following implementation code:
    ``` vbscript
    Call parser.loadFromFile(CurDir() & "/src/collabsphere.json") 
    Set job = parser.getRootobject
    ```
    This will read the collabsphere.json file content into the parser object, and then get the root object from the parser.  

    !!! warning 
        **CurDir()** returns a RUN-TIME representation of the directory from which the script is BEING RUN; not the COMPILE-TIME directory in which that the script file resides. For Visual Studio Code, this is the folder that is open.

1. Add the next block of implementation code: 
    ``` vbscript 
    sessions = helper.withCustomConstructor(sessionConstructor.withParam("title", ""))._
        withCustomConverter("debug-log", jscDebugLog.forPropertyName("debuglog"))._
        jsonArrayToObjects(job, "Session", "CollabSphere")
    ```
    There is **A LOT** going on in this single line of code. Let's break it down by chunks to see what is going on. 
    * `#!vbscript sessions = helper.withCustomConstructor(sessionConstructor.withParam("title", ""))._` 
        Here we are telling our helper object we want to use a custom constructor, and we are going to pass the value from the "title" of our JSON to our session constructor using the `withParam()` method.  The second argument to `withParam()` represents the default value to use in the event that a "title" property cannot be found.  
    * `#!vbscript     withCustomConverter("debug-log", jscDebugLog.forPropertyName("debuglog"))._`
        The `withCustomConverter() ` method will use a Converter (in this case the JsonScalarConverter instance `jscDebugLog` to convert a JSON value to a target value identified by the `forPropertyName()` method).  The reason we need to do this is because our source JSON content contains a _"debug-log"_ property, and the hyphen in that property name is **ILLEGAL** for variable names in VoltScript.  So basically this line of code tells VoltScript to grab the value of _debug-log_ from the JSON, and stuff it into our object instance's variable called _debuglog_.  It is very simple, and very effective.  
    * `#!vbscript     jsonArrayToObjects(job, "Session", "CollabSphere")`
        The `jsonArrayToObjects()` method grabs the outermost array of JSON Objects from our root JSON Object (`job`), then throws the resulting transformation from the prior lines into an array of `Session` objects.  The second argument indicates that the `Session` class can be found in the `CollabSphere` library.  The net result of this line of code is that our `sessions` Variant object now contains an _array of Session object instances_. 


### Search the sessions 
1. Add the next block of implementation code to loop through our `sessions` array:
    ``` vbscript 
    For i = Lbound(sessions) to UBound(sessions)
        If ("9F3F73226F22F82F862589EB0014CB89" = Cstr(sessions(i).id)) Then
            Set ogs = sessions(i)
            Exit For
        End If
    Next
    ```
    This code is fairly simple.  It iterates through our sessions instances and for each one tests to see if the `id` property is equal to "9F3F73226F22F82F862589EB0014CB89".  If a match is found it sets the `ogs` instance and bails out of the loop.  
    _Note: "9F3F73226F22F82F862589EB0014CB89" is the known UniversalID of the underlying session document from which the JSON was populated._  


### Print the results
1. Add the final block of implementation code to print out the results:
    ``` vbscript 
    If (ogs is Nothing) Then
        Print "We could not find the OGS of CollabSphere"
    Else
        Print "Found OGS of CollabSphere as session " & i
        Call ogs.printSummary()
    End If
    ```
    If the `ogs` instance was not found then print out that we could not find it.  Otherwise print out the summary information from the instance. 

## Run your Script
1. Use the `<command> + <shift> + <p>` sequence (`<ctrl> + <shift> + <p>` on Windows) to bring up the Command Selector.  Choose **VoltScript:Save & Run Script** (or just press enter) to save and run the script. 
1. A Secondary text box will appear.  This text box is for typing in any additional command-line parameters for the VoltScript processor.  No additional parameters are needed, so just press <enter>.  
1. Your script should now run and any output (or debug messages) should appear in the VSCode Terminal window (directly below your editor pane). Correct any errors that may be reported and continue running until you get a successful result.

??? success "The Opening General Session for ColabSphere 2023 should be:"
    * Title:   OGS101 - Opening General Session - HCL Digital Solutions NEXT
    * Time:    2023-08-30T09:00:00 - 2023-08-30T10:30:00
    * Room:    Auditorium
    * Id:      9F3F73226F22F82F862589EB0014CB89


