# Merge into existing VoltScript object

There may be occasions where you use VoltScript JSON Converter to create an object from one JSON object, but then you need to merge in elements from another. For example, imagine you have a feed of pets with IDs of the owner, and you need to merge in the owner information for each pet, like this:

```vbscript
    petsJson = |[{"name":"Captain","type":"Gentoo Penguin","ownerId":1},{"name":"Loudy","type":"Gentoo Penguin","ownerId":1},{"name":"Nimrod","type":"Gentoo Penguin","ownerId":1}]|
    ownerJson = |{"firstName":"Tom","lastName":"Popper"}|
```

The `withObject()` method allows you to handle this. You initially load the petsJson into a Variant array of Pet objects, like so:

```vbscript
Dim helper as new JsonConversionHelper()
Dim pets as Variant
pets = helper.fromJsonString(petsJson, "Pet", "deser-90.txt")
```

You need to iterate the Variant array, and then pass in each Pet to the `withObject()` method, calling `fromJson()`, passing in a JSON object.

```vbscript
Dim ownerObj as JsonObject
Dim parser as New JsonParser()
Dim firstNameConverter as New JsonScalarConverter
Dim lastNameConverter as New JsonScalarConverter

Call parser.loadFromJson(ownerJson)
Set ownerObj = parser.getRootObject
For i = 0 to UBound(pets)
    Call helper.withObject(pets(i))._
        withCustomConverter("firstName", firstNameConverter.forPropertyName("ownerFirstName"))._
        withCustomConverter("lastName", lastNameConverter.forPropertyName("ownerLastName"))._
        fromJson(ownerObj)
Next
```

!!! warning "Important"
    - You have to use the `fromJson()` method to load the content. `fromJsonString()` can't be used with `withObject()`. So you will need to convert a JSON string into a JSON object before using it.
    - In this scenario, the helper object didn't have any custom converters or label-ignores added when parsing the pets. If it had, the safest approach would have been to re-initialize the helper object before the `for` loop.
    - In rare cases, you may be able to just re-use a helper without needing to call `withObject`. But this only works if you're wanting to add to the last object the helper processed.

<!--!!! note
    

!!! info-->
    
[Example code](../../assets/example_code/deser-90.txt){: target="_new"}
<!--<a href="../../../example_code/deser-90.txt" target="_blank">Example Code</a>-->