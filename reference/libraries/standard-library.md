---
description: Stof's standard library ("std").
icon: standard-definition
---

# Standard Library

The standard library for Stof is named "std" and is automatically referenced for any function calls that do not reference another library, either by name or with a value (type).

## Console

### <mark style="color:purple;">std</mark><mark style="color:orange;">.pln</mark>(...): <mark style="color:green;">void</mark>

Prints all of the arguments given to the standard output stream.

```rust
#[main]
fn main() {
    let x = 42;
    pln("The answer to the ultimate question of life", 42);
}
```

```
> stof run example.stof
The answer to the ultimate question of life, 42
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">dbg</mark>(...): <mark style="color:green;">void</mark>

Prints all of the arguments given to the standard output stream, but in a debug form.

```rust
#[main]
fn main() {
    let mp = map([("a", "A"), ("b", "B")]);
    dbg(mp);
}
```

```
> stof run example.stof
{String("a"): String("A"), String("b"): String("B")}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">err</mark>(...): <mark style="color:green;">void</mark>

Prints all of the arguments given to the standard error stream.

```rust
#[main]
fn main() {
    err("this is to the error stream...");
}
```

```
> stof run example.stof
this is to the error stream...
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">trace</mark>(...): <mark style="color:green;">void</mark>

Prints all of the arguments given to the standard output stream, after also printing the current call stack. This enables one to trace which function calls lead up to this trace function call.

```rust
#[main]
fn main() {
    self.first();
}

fn first() {
    self.second();
}

fn second() {
    trace("calling the second function...");
}
```

```
> stof run example.stof
trace main & root ...
trace first & root ...
trace second & root ...
calling the second function...
```

## Throw & Assert

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">throw</mark>(type?: <mark style="color:green;">str</mark>, message: <mark style="color:green;">str</mark>): <mark style="color:green;">void</mark>

Throws an error in Stof. Errors can be caught in Stof with a try-catch block.

{% hint style="info" %}
See [error-handling.md](../../common-concepts/error-handling.md "mention") for more information on handling errors in Stof. Also check out [stof-language.md](../stof-language.md "mention") for a reference on the try-catch statement.
{% endhint %}

```rust
#[main]
fn main() {
    // The first "type" parameter is optional - the default error type is "Std"
    throw("MyError", "this is an error");
}
```

```
> stof run example.stof
error @ root main ...
    unwind main @ root ...
    MyError: this is an error
```

```rust
#[main]
fn main() {
    try {
        throw("this is an error");
    } catch (error: (str, str)) { // message only if type is "str"
        assertEq(error, ("Std", "this is an error"));
        pln("no errors here");
    }
}
```

```
> stof run example.stof
no errors here
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assert</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not truthy.

| Stof Type   | Truthy          |
| ----------- | --------------- |
| bool        | value == true   |
| int & float | value ≠ 0       |
| string      | value.len() > 0 |
| null & void | false           |
| vec         | true            |
| map         | true            |
| set         | true            |
| fn          | true            |
| obj         | true            |
| tuple       | true            |
| blob        | true            |

```rust
#[test]
#[errors]
fn test() {
    assert(false);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertNot</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is truthy.

```rust
#[test]
#[errors]
fn test() {
    assertNot(true);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertEq</mark>(a: <mark style="color:green;">unknown</mark>, b: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "a" does not equal "b".

```rust
#[test]
fn assertPasses() {
    assertEq(42, 42.0);
}

#[test]
#[errors]
fn assertErrors() {
    assertEq("hi", "hello");
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertNeq</mark>(a: <mark style="color:green;">unknown</mark>, b: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "a" equals "b".

```rust
#[test]
fn assertPasses() {
    assertNeq(1, 2);
}

#[test]
#[errors]
fn assertErrors() {
    assertNeq("a", "a");
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertNull</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not null.

```rust
#[test]
#[errors]
fn test() {
    assertNull(23);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertObject</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not an object.

```rust
#[test]
fn test() {
    assertObject(new {});
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertArray</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not an array ("vec" type).

```rust
#[test]
fn test() {
    assertArray([1, 2, 3]);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertTuple</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not a tuple.

```rust
#[test]
fn test() {
    assertTuple((2, "hi"));
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">assertNumber</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Throws an error if "value" is not a number ("int" or "float" type).

```rust
#[test]
fn test() {
    assertNumber(42);
}
```

## Helper Functions

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">or</mark>(...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) parameter, or null if all parameters are empty. This function is a handy replacement for the null check "??" operator in other languages. This function is also implemented for every Stof value type library.

```rust
other: "dude"

#[test]
fn example() {
    let var = self.field.or(self.other, self.other2, "unknown");
    assertEq(var, "dude");
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">box</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">Box\<unknown></mark>

Boxes this value if the value is not already boxed. See [primitive-types.md](../../common-concepts/primitive-types.md "mention")for more information on the Box type.

```rust
fn addOne(value: Box<int>) {
    value += 1;
}

#[test]
fn test() {
    let val = box(5);
    self.addOne(val);
    assertEq(val, 6);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">unbox</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark>

Unboxes the value if the value is boxed (copies the value). For collections, this function will recursively unbox values. This function is not normally needed as casts will take care of this behavior when needed.

```rust
fn addOne(value: Box<int>) {
    value += 1;
}

#[test]
fn test() {
    let val = box(5);
    self.addOne(val);
    assertEq(val, 6);
    
    let other = unbox(val);
    self.addOne(other);
    assertEq(other, 6);
}
```

```rust
fn addOne(value: int): int {
    return value + 1;
}

#[test]
fn test() {
    let val = box(5);
    val = self.addOne(val);
    assertEq(val, 6);
    
    // val is still boxed because assignments to a boxed value sets that value
    // for everyone with a reference.
    
    let other = self.addOne(val);
    assertEq(val, 6);
    assertEq(other, 7);
    
    // other is not boxed here
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">data</mark>(path: <mark style="color:green;">str</mark>): <mark style="color:green;">data</mark>

Calls "from" on the [Data Library](data-library.md), returning a new opaque pointer to a field or function. Returns null if a field or function is not found at the requested location.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isNumber</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed number type. Number types include "float", "int", or any unit type ("float" variant). This is similar to "typeof" and "typename", but is more generic. If you want to know more specifically what kind of number the value is, use these statements in combination with the [Number Library](number-library.md) helper functions (specific unit types).

{% hint style="info" %}
See the [stof-language.md](../stof-language.md "mention") for more information on the "typeof" and "typename" expressions.
{% endhint %}

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isObject</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed object type. This is similar to "typeof" and "typename", however, "typename" results in the complex object type if present and "typeof" will return the box signature if boxed. This is simpler and a bit cleaner in practice.

{% hint style="info" %}
See the [stof-language.md](../stof-language.md "mention") for more information on the "typeof" and "typename" expressions.
{% endhint %}

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isNull</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed null type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isString</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed string type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isBool</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed boolean type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isMap</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed map type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isSet</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed set type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isBlob</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed blob type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isArray</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed array/vector type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isVec</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

This function is an alias for isArray.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isFunc</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is either a boxed or unboxed function type.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isBoxed</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Returns true if the value is boxed.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">isBox</mark>(value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

This function is an alias for isBoxed.

## Collection Initialization

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">vec</mark>(...): <mark style="color:green;">vec</mark>

An alternative way to construct an array/vector. Will push each argument to a new vector and return that vector. A key difference between this function and the "\[..]" syntax is that when another vector or set is passed into this function, each value of that vector or set will be pushed to the new vector instead of the entire vector/set as a value.

```rust
#[test]
fn test() {
    let array = vec(1, 2, [3, 4], set(5, 6));
    assertEq(array, [1, 2, 3, 4, 5, 6]);
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">set</mark>(...): <mark style="color:green;">set</mark>

This is how sets are constructed in Stof. Just like for vectors, each argument will be inserted into a new set. If another set or vector is passed into this function, each value of that set or vector will get inserted as opposed to the entire vector/set as a value. If you're looking to do this, use the "Set.insert(value: unknown)" function after constructing a new set.

```rust
#[test]
fn test() {
    let a = set(1, 2, 3);
    let b = set(2, 3, 4);
    assertEq(a.intersection(b), set(2, 3));
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">map</mark>(...): <mark style="color:green;">map</mark>

This is how maps are constructed in Stof. Each argument will be inserted into a new map.

Valid arguments include:

* Vector(s) of tuples, in the form (key: unknown, value: unknown)
  * Example: `map([("a", 100), ("b", 200)])`
* Another map(s), where all elements of that map will be copied into the new map
* Tuple(s) in the form (key: unknown, value: unknown)
  * Example: `map(("a", 100), ("b", 200))`&#x20;
* Object(s), where each field will be inserted into the map, and unioned where keys collide
  * Every sub-object field will be recursively inserted as a map

```rust
#[test]
fn example() {
    let a = map([("a", "A"), ("b", "B")]);
    assertEq(a, map(a));
}
```

## Formats & Data  Functions

Stof documents have a dynamic set of formats that they can use. The following functions use those formats to interact with the document. These formats are also referenced for imports, so any format listed below can be imported with the Stof "import" statement as well.

It is up to the host environment to load formats - they do not get serialized or travel with documents! Formats can always be added or replaced by the host environment.

Formats are referenced by ID. At any time, one can see what format IDs are loaded with the "std.formats()" function. However, these are the formats Stof provides by default:

* text - loads a string into a field named "text" on the object referenced.
* bytes - loads a blob into a field named "bytes" on the object referenced.
  * This format is the fallback format for "header\_import" which is an important document functionality used in "parse" for parsing binary streams into a Stof document.
* Stof - the format we are documenting currently, the language and everything in these docs.
  * It is treated as a format just like all of the others, can be parsed, blobified, etc.
* JSON - we all know and love this one - used in a lot of places, the JavaScript Object Notation is completely supported in Stof. One can stringify, blobify, and parse JSON at any time.
* TOML - complete support for importing, stringify, blobify, etc. for TOML.
* YAML - support for importing, stringify, blobify, etc. for YAML.
* XML - support for importing, stringify, blobify, etc. for XML.
  * XML has a lot of specializations that Stof does its best with, however, creating a custom XML format if needed is pretty easy - you can always add or replace these formats.
* urlencoded - support for URL-encoded messages.
* bstof - a binary Stof document that has already been created/parsed.
  * Helpful for transferring documents and interfaces (fields, types, etc...) over the wire or saving them for later use.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">parse</mark>(data: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, format: <mark style="color:green;">str</mark>, location?: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

This function enables users to parse additional data into the document at runtime, including additional Stof data/interfaces.

#### Parameters

* data - the data to parse into the document, either as a string or as a blob (Uint8Array/Vec\<u8>).
  * If the data is a string, the document will get a call to "string\_import".
  * If the data is a blob, the document will get a call to "header\_import".
    * This import is special, because each format also has a "content-type". When this import method is used, the format can be a "content-type" header, which is useful for parsing HTTP responses (makes this really easy).
* format - a string identifier pointing to a loaded format to use for parsing this data.
  * The default format is "stof", but you might use "json" if the data is a JSON string for example.
* location - an optional location in the document to parse the additional data into.
  * The default location is the current object/scope where this function is called.
  * The location given does not have to exist, Stof will create additional objects where needed.

```typescript
#[test]
fn example() {
    parse('{"field": "hello"}', "json");
    assertEq(self.field, "hello");

    parse('another = "dude"', "toml");
    assertEq(self.another, "dude");
    
    parse("fn hello(): str { return 'hello, world'; }", "stof", "self.subobj");
    assertEq(self.subobj.hello(), "hello, world");
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">blobify</mark>(data: <mark style="color:green;">obj</mark> | <mark style="color:green;">str</mark> | <mark style="color:green;">vec</mark>, format: <mark style="color:green;">str</mark>): <mark style="color:green;">blob</mark>

A blob is a byte array in Stof (Uint8Array/Vec\<u8>). This is much more efficient than a vector of number values.

The blobify function is a helper for turning data into a blob. Users can cast vectors or strings directly into a blob (the default for strings is a UTF-8 representation) with the "as" keyword or by specifying a type. However, for objects, this function executes "export\_bytes" with the document, allowing a specific format to interpret the object and turn it into a blob.

For example, a document might have a specific encoding format loaded, which would interpret the object's fields differently.

This is useful when sending binary messages over the wire, or when working with files or streams of data. The blob type is also a good way to store unstructured data in Stof.

```typescript
#[test]
fn normalCasts() {
    let msg = "hello, world";
    
    // Three ways to turn this string into a UTF-8 blob
    let first = msg as blob;
    let second: blob = msg;
    let third = blobify(msg); // format doesn't matter here
    
    assertEq(msg, "hello, world");
    assertEq(first, second);
    assertEq(second, third);
    
    assertEq(first as str, "hello, world");
}

#[test]
fn objects() {
    let object = new {
        field: "hello"
    };
    let message = blobify(object, "json"); // json format just uses UTF-8 also...
    assertEq(message as str, '{"field":"hello"}');
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">stringify</mark>(data: <mark style="color:green;">obj</mark>, format: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

The stringify function works just like in other languages, however, Stof documents have a dynamic set of formats that can be used (referenced above). This function is supported by any format that implements "export\_string", which is all default formats except for "bstof", a binary-only format.

```typescript
object: {
    message: "this is a string"
    valid: true
}

#[test]
fn test() {
    let string = stringify(self.object, "json");
    assertEq(string, '{"message":"this is a string","valid":true}');
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">hasFormat</mark>(format: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Does this document have a format loaded with this format ID?

```rust
#[test]
fn test() {
    assert(hasFormat("json"));              // json is a default format Stof provides
    assertNot(hasFormat("mycustomformat")); // false unless you provide it...
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">formats</mark>(): <mark style="color:green;">vec</mark>

Returns a vector of all of the currently loaded formats.

```rust
#[test]
fn test() {
    let formats = set(formats()); // set just makes them sorted for eq...
    assertEq(formats, set(
        "bytes",
        "stof",
        "urlencoded",
        "yaml",
        "xml",
        "bstof",
        "json",
        "text",
        "ndjson",
        "toml"
    ));
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">formatContentType</mark>(format: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

All formats loaded into Stof have the opportunity to provide a content type. This content type is intended to be used to create headers when sending data of a specific format over the network. For example, you might want to add a "Content-Type" header when using HTTP - this function will provide the value you should use for it. A format's content type will default to "text/plain".

```rust
#[test]
fn test() {
    assertEq(formatContentType("json"), "application/json");
    assertEq(formatContentType("urlencoded"), "application/x-www-form-urlencoded");
}
```

## Libraries

Just like for formats, Stof supports a dynamic set of libraries that the host can optionally provide. These do not get serialized with documents and do not travel with them over the wire. It is always up to the host environment to give Stof access to the outside world - libraries are the way to do it.

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">hasLibrary</mark>(lib: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Does this document have a library loaded with this library identifier?

```rust
#[test]
fn test() {
    assert(hasLibrary("std")); // the standard library we are currently documenting..
    assert(hasLibrary("Number"));
    assertNot(hasLibrary("CustomLibrary")); // false unless you provide it
}
```

### <mark style="color:purple;">std</mark>.<mark style="color:orange;">libraries</mark>(): <mark style="color:green;">vec</mark>

Returns a vector of all currently loaded libraries.

```rust
#[test]
fn test() {
    let libs = set(libraries()); // set just keeps it ordered for eq
    assertEq(libs, set(
        "std",
        "Array",
        "Blob",
        "Bool",
        "Function",
        "Map",
        "Set",
        "Number",
        "Object",
        "String",
        "Tuple",
        "fs"        // File System Library added by the CLI for imports, etc...
    ));
}
```
