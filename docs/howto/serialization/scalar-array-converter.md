# Use the JsonArrayConverter

## Scalar array converter

Often a class may contain arrays. You saw that with deserialization, a class member variable thats expected to receive an array must be declared as a Variant. This means that it may or may not be an array. Imagine a class with this code:

```vbscript
Class Session

    Public id as String
    Public title as String
    Public products as Variant

End Class
```

Assuming that the products variable is only used to hold scalars, such as an array of Strings, you can just use a **JsonArrayConverter**. So the instance of the class can be created like so:

```vbscript
Dim sess as New Session
sess.id = "Ad16"
sess.title = "Colaborate around Business Applications in Communities"
sess.products = Split("Domino,Connections,Volt", ",")
```

The object can be deserialized by adding a **JsonScalarConverter** for `id` and `title`, and a **JsonArrayConverter** for `products`.

```vbscript
Call helper.withScalarConverter("id").withScalarConverter("title").withArrayConverter("products")
json = helper.toJsonString(sess, true)
```

## Forcing arrays

The `JsonArrayConverter` will return an array regardless of the source. So even if the instance of the Session class is created like so:

```vbscript
sess.id = "Ad01"
sess.title = "Domino HA"
sess.products = "Domino"
```

The JSON returned will still be an array:

```json
{
  "id": "Ad01",
  "products": [
    "Domino"
  ],
  "title": "Domino HA"
}
```

## Class with array member variable

Currently with VoltScript JSON Converter, member variables like `product` that are expected to receive arrays need to be declared as Variants. Otherwise a compiler error "Illegal reference to array or list" will be thrown when the code to write to the member variable is executed. This also makes it easier to write to the class. If the member variable is declared as `Public products() as String`, you can no longer just use `Split()` directly. Instead, you need to split and iterate the result, and then add each element in the array in turn:

```vbscript
Dim temp as Variant
Dim i as integer

temp = Split("Domino,Connections,Volt", ",")
Redim sess.products(UBound(temp))
For i = 0 to UBound(temp)
    sess.products(i) = temp(i)
Next
```

Serialization will still work the same though. The helper can still be set up with `Call helper.withScalarConverter("id").withScalarConverter("title").withArrayConverter("products")`.

However, the `JsonScalarConverter` will also return an array, if the source is an array. So the helper will still have the same result if set up with `Call helper.withScalarConverter("id").withScalarConverter("title").withScalarConverter("products")`.

An array will also be returned if the object is created with the following code:

```vbscript
sess.id = "Ad01"
sess.title = "Domino HA"
Redim sess.products(0)
sess.products(0) = "Domino"
```

This is because an array check on the `products` variable will return `true`, it's an array under all circumstances.

[Example code](../../assets/example_code/ser-20.txt){: target="_new" rel="noopener noreferrer"}
