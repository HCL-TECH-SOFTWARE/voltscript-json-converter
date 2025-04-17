# Extend CollabSphere Parsing

## Pre-Requisites

1. Complete [Parsing Nobel Laureates](laureates.md)
1. Complete [Parsing CollabSphere Sessions](collabsphere_1.md)

## Objectives

1. Remove unnecessary variables from the Session class.
1. Add additional arguments to the Session class Constructor
1. Add dynamic getter properties to the Session class.
1. Make use of the VoltScript `return` statement.
1. Make use of the VoltScript `||` short-circuiting **or** operator.
1. Extend your knowledge of helper methods within the VoltScript Json Converter Library
1. Create additional Custom Converters

## Script Revision

### Initial Updates

1. Launch your VSCode IDE, then open your working project used for the previous tutorials.
1. Within the IDE Explorer, copy and paste your `CollabSphere.vss` file as `CollabSphere_2.vss`.
1. Find the following block of code (in `Sub Initialize`):

    ``` vbscript
    sessions = helper.withCustomConstructor(sessionConstructor.withParam("title", ""))._
        withCustomConverter("debug-log", jscDebugLog.forPropertyName("debuglog"))._
        jsonArrayToObjects(job, "Session", "CollabSphere")
    ```

    and change `CollabSphere` to `CollabSphere_2`.
1. Save and Run the Script.  Your output should be identical to that when running the script from the previous tutorial.

### Modify the Session Class

!!! tip "Clean Code is Good Code"

1. Find the `Session` class within your code, and remove the Public String variables `debuglog`, `backgroundColor`, `textColor`, and `className`.  These variables were initially created to allow for simple importation from the underlying JSON content, but they will never be used, so there is no reason to keep them.  
1. Change the Public String variable names from `id, start`, and `end` to `unid, startTime`, and `endTime` respectively.
1. Revise the constructor to accept and additional initial argument `unid`, and use it to set the Object's `unid` value.
1. Add the **Private** String variables `sessiondate_` and `sessiontime_` to the declarations area of the `Session` class.  
1. Add the following getter property:

    ``` vbscript
    Property Get SessionDate As String 
        Dim chunks as Variant 

        If (Len(Me.sessiondate_) > 0) Then return Me.sessiondate_
    
        chunks = split(Me.startTime, |T|)
        if (ubound(chunks) > 0) Then Me.sessiondate_ = cstr(chunks(0))

        return Me.sessiondate_ 
    End Property
    ```

    This code block makes use of the VoltScript `return` statement in line 4.  In this situation, the `return` statement immediately ceases processing of the property script and returns the value of `Me.sessiondate_` to the calling code, which alleviates needing to:
    1. Explicitly set the value of `SessionDate` to that of `Me.sessiondate_`.
    1. Add an `Else` and `End If` to the `If` statement, or explicitly call `Exit Property`.

    ??? info "short-circuit comparisons"
        In LotusScript (from which VoltScript evolved), `And` and `Or` comparisions **always** evaluate every condition (both sides of the comparison operator), which can result in ineffecient code (or sometimes code failures).   _Short Circuit_ comparisons (`||` for `Or`, and `&&` for `And`) will cease comparison at the first condition which logically ends the need for additional comparisons, and will not evaulate additional conditions. The following code example should help:

        ``` vbscript
        Dim a as Integer
        Dim b as Integer
        Dim c as Integer

        a = 1 
        b = 2 
        c = 3 

        If (a < b) || (b < c) Then Print "True: evaluated first condition, skipped second condition"
        If (a < b) && (b < c) Then Print "True: evaluated both conditions"
        If (a > b) || (b > c) Then Print "False: evaluated both conditions"
        If (a > b) && (b > c) Then Print "False: evaluted first condition, skipped second condition"
        ```

1. Add the following getter property:

    ``` vbscript
    Property Get SessionTime As String 
        Dim chunks as Variant 
        Dim strTemp as String 

        If (Len(Me.sessiontime_) > 0) Then return Me.sessiontime_
        
        If (Len(Me.SessionDate) < 1) || (Len(Me.endTime) < 1) Then return

        chunks = split(Me.startTime, |T|)
        strTemp = Left$(chunks(Ubound(chunks)), 5)

        chunks = split(me.endTime, |T|)
        chunks(0) = strTemp 
        strTemp = Left$(chunks(Ubound(chunks)), 5)
        chunks(1) = strTemp 
        Me.sessiontime_ = Join(chunks, | - |)

        return Me.sessiontime_ 
    End Property 
    ```

    As in the previous step, this code also makes use of the `return` statment.  However, it does something rather interesting.  If you take a closer look at line 6:

    ``` vbscript
    If (Len(Me.SessionDate) < 1) || (Len(Me.endTime) < 1) Then return
    ```

    You will notice the use of a double-pipe (`||`).  In both LotusScript and VoltScript the use of a double-pipe can indicate an empty string (just as `{}` or `""` does).  In VoltScript, this double-pipe _also_ indicates a _short-circuit_ `Or` comparison, **depending upon the context** in which it appears.

    Because this line of code is performing logical comparison operations (an `If` statment) the VoltScript processor recognizes that the double-pipe is a comparison operator, not an empty string indicator.

    You will also notice that line 5 ends with a `return` statement, but has no following value.  This causes processing of the current block to immediately stop and return to the calling code.  Because the block is a `Property` that would normally return a String, the default empty string `""` value is returned to the calling code.

    !!! tip
        The use of `return`, `||`, and `&&` statments result in cleaner, more efficient, and easier to understand code.  

1. Revise the `printSummary` method to print the values of `SessionDate` and `SessionTime`, replace `unid` with `id`, and to no longer print values for `start` or `end`.

??? example "Revised Session class"

    ``` vbscript
    Class Session
        Private sessiondate_ As String 
        Private sessiontime_ As String 
        Public unid As String 
        Public title As String
        Public room As String 
        Public startTime As String
        Public endTime As String

        Sub New(unid as String, title as String)
            Me.unid = unid
            Me.title = title 
        End Sub

        Property Get SessionDate As String 
            Dim chunks as Variant 

            If (Len(Me.sessiondate_) > 0) Then return Me.sessiondate_
        
            chunks = split(Me.startTime, |T|)
            if (ubound(chunks) > 0) Then Me.sessiondate_ = cstr(chunks(0))

            return Me.sessiondate_ 
        End Property 

        Property Get SessionTime As String 
            Dim chunks as Variant 
            Dim strTemp as String 

            If (Len(Me.sessiontime_) > 0) Then return Me.sessiontime_
            If (Len(Me.SessionDate) < 1) || (Len(Me.endTime) < 1) Then return || 

            chunks = split(Me.startTime, |T|)
            strTemp = Left$(chunks(Ubound(chunks)), 5)

            chunks = split(me.endTime, |T|)
            chunks(0) = strTemp 
            strTemp = Left$(chunks(Ubound(chunks)), 5)
            chunks(1) = strTemp 
            Me.sessiontime_ = Join(chunks, | - |)

            return Me.sessiontime_ 
        End Property 

        Sub printSummary()
            Print "Title:   " & Me.title 
            Print "Date:    " & Me.SessionDate
            Print "Time:    " & Me.SessionTime 
            Print "Room:    " & Me.room  
            Print "Unid:    " & Me.unid
        End Sub

    End Class
    ```

### Modify code that uses VoltScript Json Helper methods

1. Find the following block of code (in `Sub Initialize`):

    ``` vbscript
    sessions = helper.withCustomConstructor(sessionConstructor.withParam("title", ""))._
        withCustomConverter("debug-log", jscDebugLog.forPropertyName("debuglog"))._
        jsonArrayToObjects(job, "Session", "CollabSphere_2")
    ```

1. Insert the code `.ignoreLabel("debug-log")` between `sessions = helper` and `.withCustomConstructor`.  The `.ignoreLabel()` method instructs the **JsonConverterHelper** object instance (`helper`) to ignore the specified label (and by extension the label's associated value) when processing the JSON content.  In this case we no longer want to use the `debug-log` elements within the JSON content.
1. Find the block of code `withCustomConverter("debug-log", jscDebugLog.forPropertyName("debuglog"))._` and remove it entirely.  Because we have instructed the helper object to ignore the `debug-log` elements, we no longer have any need to convert their values to the target objects's `debuglog` variables (which we removed as the first step in modifying the Session class).
1. Find the code `jsonArrayToObjects` and immediately prior to it insert additional `.ignoreLabel()` method calls for the other Public String variables that we previously removed (`backgroundColor`, `textColor`, and `className`).

### Modify code that uses VoltScript Json Converter methods

1. Because we have renamed the variables `start` and `end` in the `Session` class to `startTime` and `endTime`, we need perform some conversions when processing the JSON into our object instances -similar to the conversion for `debug-log` that we have removed.  In the declarations section of `Sub Initialize`, find the line of code `Dim jscDebugLog As New JsonScalarConverter()`, and replace it with the following:

    ``` vbscript
    Dim jscStart As New JsonScalarConverter()
    Dim jscEnd As New JsonScalarConverter()
    ```

The reason for this is that each unique conversion to be performed requires a distinct converter object instance.

1. Insert the following code immediately prior to `jsonArrayToObjects(job, "Session", "CollabSphere_2")`

    ``` vbscript
    withCustomConverter("start", jscStart.forPropertyName("startTime"))._
    withCustomConverter("end", jscEnd.forPropertyName("endTime"))._
    ```

This tells the code to take the JSON values from `start` and `end` and assign them to the object's variables `startTime` and `endTime`.  

??? example "Loading the sessions"
    ``` vbscript
    sessions = helper.ignoreLabel("debug-log")._
        ignoreLabel("backgroundColor")._
        ignoreLabel("textColor")._
        ignoreLabel("className")._
        withCustomConstructor(sessionConstructor.withParam("id","").withParam("title", ""))._
        withCustomConverter("start", jscStart.forPropertyName("startTime"))._
        withCustomConverter("end", jscEnd.forPropertyName("endTime"))._
        jsonArrayToObjects(job, "Session", "CollabSphere_2")
    ```

### Revise the search logic

1. This final change is a very simple one.  Find the line of code

    ``` vbscript
    If ("9F3F73226F22F82F862589EB0014CB89" = Cstr(sessions(i).id)) Then
    ```

    and change the `.id` reference to `.unid`.  

## Run your Script

1. Use the `<command> + <shift> + <p>` sequence (`<ctrl> + <shift> + <p>` on Windows) to bring up the Command Selector.  Choose **VoltScript:Save & Run Script** (or just press enter) to save and run the script.
1. A Secondary text box will appear.  This text box is for typing in any additional command-line parameters for the VoltScript processor.  No additional parameters are needed, so just press <enter>.  
1. Your script should now run and any output (or debug messages) should appear in the VSCode Terminal window (directly below your editor pane). Correct any errors that may be reported and continue running until you get a successful result.

??? success "The Opening General Session for ColabSphere 2023 should be:"
    - Title:   OGS101 - Opening General Session - HCL Digital Solutions NEXT
    - Date:    2023-08-30
    - Time:    09:00 - 10:30
    - Room:    Auditorium
    - Unid:    9F3F73226F22F82F862589EB0014CB89

!!! question "Challenge Yourself"

    For more advanced VoltScript, try the following:

    - Add a getter property to the session class that will extract the CollabSphere session code from the beginning of the title. 
    - Revise the search logic to search for a specific session code instead of a UniversalID 
    - Revise the SessionDate property of the session to include the name of the month
    - Add a getter property to the session class that returns the title with the session code removed.

??? example "Challenge VoltScript"
    ``` vbscript
    %REM
        Copyright 2022-2023 HCL America, Inc.
        Licensed under the Apache License, Version 2.0 (the "License");
        you may not use this file except in compliance with the License.
        You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

        Unless required by applicable law or agreed to in writing, 
        software distributed under the License is distributed on an "AS IS" BASIS, 
        WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
        See the License for the specific language governing permissions and limitations under the License	
    %END REM

    Option Public
    Option Declare
    Use "../src/VoltScriptJsonConverter"

    Property Get MonthNames as Variant 
        Static result(1 to 12) as String 
        Static isLoaded as Boolean 

        If Not isLoaded Then 
            result(1) = |January|
            result(2) = |February|
            result(3) = |March|
            result(4) = |April|
            result(5) = |May|
            result(6) = |June|
            result(7) = |July|
            result(8) = |August|
            result(9) = |September|
            result(10) = |October|
            result(11) = |November|
            result(12) = |December|
            isLoaded = True 
        End If 

        MonthNames = result 
    End Property 

    Class Session
        Private id_ as String 
        Private sessiondate_ As String 
        Private sessiontime_ As String 
        Public unid as String
        Public title as String
        Public startTime as String
        Public endTime as String
        Public room as String 

        Public Property Get Id as String 
            Dim chunks as Variant 

            If (Len(me.id_) < 1) Then 
                chunks = split(Me.title, | - |)
                Me.id_ = Trim$(chunks(0))
            End If 

            Id = Me.id_ 
        End Property 

        Property Get SessionDate As String 
            Dim chunks as Variant 
            Dim dateparts(2) As String 

            If (Len(Me.sessiondate_) > 0) Then return me.sessiondate_
        
            chunks = split(me.startTime, |T|)
            If (ubound(chunks) > 0) Then 
                chunks = split(chunks(Lbound(chunks)), |-|)

                dateParts(0) = chunks(2)
                dateParts(1) = MonthNames(cint(chunks(1)))
                dateParts(2) = chunks(0)
                me.sessiondate_ = Join(dateParts, | |)
            End If 

            return me.sessiondate_ 
        End Property 

        Property Get SessionTime As String 
            Dim chunks as Variant 
            Dim strTemp as String 

            If (Len(Me.sessiontime_) > 0) Then return me.sessiontime_
            
            If (Len(me.SessionDate) < 1) || (Len(Me.endTime) < 1) Then return

            chunks = split(me.startTime, |T|)
            strTemp = Left$(chunks(Ubound(chunks)), 5)

            chunks = split(me.endTime, |T|)
            chunks(0) = strTemp 
            strTemp = Left$(chunks(Ubound(chunks)), 5)
            chunks(1) = strTemp 
            Me.sessiontime_ = Join(chunks, | - |)

            return me.sessiontime_ 
        End Property 

        Property Get SessionName As String 
            If (len(Me.Id) > 0) Then 
                return Trim$(Mid$(Me.title, Len(Me.Id & | - |), Len(Me.title)))
            Else 
                return  Me.title  
            End If 
        End Property 

        Sub New(unid as String, title as String)
            Me.unid = unid
            Me.title = title 
        End Sub

        Sub printSummary()
            Print "Id:      " & Me.Id
            Print "Session: " & Me.SessionName 
            Print "Date:    " & Me.SessionDate
            Print "Time:    " & Me.SessionTime 
            Print "Room:    " & Me.room  
            Print "Unid:    " & Me.unid
        End Sub
    End Class

    Sub Initialize
        Dim job As JsonObject

        Dim parser As New JsonParser()
        Dim helper As New JsonConversionHelper()
        Dim sessionConstructor As New JsonCustomConstructor()
        Dim jscStart As New JsonScalarConverter()
        Dim jscEnd As New JsonScalarConverter()
        Dim sess As Session

        Dim sessions As Variant
        Dim i As Integer

        Call parser.loadFromFile(CurDir() & "/samples/collabsphere.json") 
        ' CurDir() as referenced above is set at RUN-TIME
        ' it represents the directory from which the script is BEING RUN; 
        ' not the COMPILE-TIME directory in which that the script file resides.
        
        Set job = parser.getRootobject

        ' jscStart.forPropertyName and jscEnd.forPropertyName are both run before either withCustomConverter is run
        ' So if we use the same variable, both custom converters write to endTime
        sessions = helper.ignoreLabel("className")._
            ignoreLabel("debug-log")._
            ignoreLabel("backgroundColor")._
            ignoreLabel("textColor")._
            ignoreLabel("className")._
            withCustomConstructor(sessionConstructor.withParam("id","").withParam("title", ""))._
            withCustomConverter("start", jscStart.forPropertyName("startTime"))._
            withCustomConverter("end", jscEnd.forPropertyName("endTime"))._
            jsonArrayToObjects(job, "Session", "CollabSphere_challenge")

        For i = Lbound(sessions) to UBound(sessions)
            ' use Id Property 
            If (|DEV113| = Cstr(sessions(i).Id)) Then
                Set sess = sessions(i)
                Exit For
            End If
        Next

        If (sess is Nothing) Then
            Print "We could not find the VoltScript Unit Testing"
        Else
            Print "Found VoltScript Unit Testing! " & i
            sess.printSummary
        End If
    End Sub
    ```