---
description: Stof's standard data library ("Data").
icon: file-circle-question
---

# Data Library

{% hint style="info" %}
Take a look at [how Stof works](../../book/introduction-and-design.md) before digging into the data library.
{% endhint %}

Stof has two main data types (not value types, data types): fields and functions. Fields hold Stof values and functions operate on values. However, one can add additional data types to Stof and organize them just like fields and functions.

The "data" value type is an opaque reference to additional, custom data types. Fields can hold this value type (of course) and functions can then operate on/with them.

For example, if you created a PDF data type and a [library](./) to go with it, instead of holding the PDF file as a "blob" value, you could reference the PDF data (in any form you'd like) with a "data" value, pointing to your custom data type so that you may work with it in your library however you wish from the existing functional interface.

Because fields and functions are just data, this library also applies to them (but primarily intended for the above purpose).

```rust
field: 42

object: {
    ref super.field;
}

another: {}

#[test]
fn test() {
    let dref = Data.from("self.field");
    assertEq(dref.objects().len(), 2);

    self.another.reference("super.field");
    assertEq(dref.objects().len(), 3);

    self.field = 100;
    assertEq(self.another.field, 100);
    assertEq(self.object.field, 100);
}
```

## Common Value Functions

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.toString</mark>(ref: <mark style="color:green;">data</mark>): <mark style="color:green;">str</mark>

Returns a string formatted to let you know it's a data reference, with the ID of the data it references.

```typescript
field: 42

#[test]
fn test() {
    let data = data('self.field');
    pln(data.toString()); // data("dtaSt1Q5yZUGpeShVOFCyGGt")
}
```

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.or</mark>(ref: <mark style="color:green;">data</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

## Constructors

{% hint style="info" %}
A helper function "std.data(..)" exists in the [Standard Library](standard-library.md) ("std") that is shorthand for calling "Data.from(..)".
{% endhint %}

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.from</mark>(path: <mark style="color:green;">str</mark>): <mark style="color:green;">data</mark>

Returns an opaque reference to either a field or function at the requested path or null if a field or function is not found.

For custom data types, this function will be replaced with a custom library to either create or reference some other data.

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.fromId</mark>(id: <mark style="color:green;">str</mark>): <mark style="color:green;">data</mark>

If you know the ID of the data you'd like to reference, use this function to create an opaque value that references the Stof data.

```rust
field: 42

#[test]
fn test() {
    let dref = Data.from("self.field");
    let another = Data.fromId(dref.id());
    assertEq(dref, another);
}
```

## Data Functions

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.exists</mark>(ref: <mark style="color:green;">data</mark>): <mark style="color:green;">bool</mark>

Returns true if the data reference points to existing data in the Stof graph.

{% hint style="danger" %}
Keep in mind it is possible to have dangling references, and script accordingly. However, if the data exists, it will always be attached to at least one object/node.
{% endhint %}

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.id</mark>(ref: <mark style="color:green;">data</mark>): <mark style="color:green;">str</mark>

Returns the ID of this data reference, whether it is dangling or not.

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.objects</mark>(ref: <mark style="color:green;">data</mark>): <mark style="color:green;">vec</mark>

Returns an array of all objects that this data reference is attached to (empty if data doesn't exist).

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.drop</mark>(ref: <mark style="color:green;">data</mark>, object?: <mark style="color:green;">obj</mark>): <mark style="color:green;">bool</mark>

Remove this data, or optionally remove only from a specific object. Returns true if the data was removed.

If an object argument is provided and the data only exists on that object, it will be removed altogether from the document. Otherwise, the object will no longer reference the data, but the data will remain for the other objects that still reference it.

If no object argument is given, the data will be removed from all objects that reference it and dropped from Stof altogether.

```rust
field: "hello";
a: {
    ref super.field;
}
b: {
    ref super.field;
}

#[test]
fn from_object() {
    let dref: data = Data.from("self.field");
    assertEq(dref.objects().len(), 3);
    dref.drop(self.a); // drop the data from a

    assertEq(dref.objects().len(), 2);
    assertNull(self.a.field);

    dref.drop(); // drop from everywhere!
    assertNull(self.field);
    assertNot(dref.exists());
}
```

### <mark style="color:purple;">Data</mark><mark style="color:orange;">.attach</mark>(ref: <mark style="color:green;">data</mark>, object: <mark style="color:green;">obj</mark>): <mark style="color:green;">bool</mark>

Returns true if the data exists and is newly attached to the provided object. Will return false if the data doesn't exist or if the object already references the data.

```rust
field: "hello";
a: {}

#[test]
fn to_object() {
    let dref = Data.from("self.field");
    assertEq(dref.objects().len(), 1);
    
    dref.attach(self.a); // same as 'Object.reference' in this case (with a field)
    assertEq(dref.objects().len(), 2);
    assertEq(self.a.field, "hello");
}
```
