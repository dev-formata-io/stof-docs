---
description: Stof's primitive value types.
---

# Primitive Types

Every value in Stof is of a certain data type, which lets Stof know how to work with that data. There are two data types: scalar and compound. Stof also has a Box concept to allow passing values by reference and an "unknown" type to allow functions to accept many different value types.

## Scalar Types

A scalar type represents a single value. Stof has 5 primary scalar types: integers, floating-point numbers, booleans, strings, and blobs.

### Integers

An integer is a number without a fractional component. Stof has one integer type, which is an "int". This is implemented as an i64, which is a signed 64-bit integer and is very practical for how Stof is used.

{% hint style="info" %}
For added readability, integers can be prefixed with either a "+" or "-" and can contain as many "\_" characters as you wish. For example, `+1_000_000` will equal 1000000.
{% endhint %}

```rust
int field: 42; // i64 field named "field" - "int" is optional here

fn example(): int {
    return 42; // i64
}
```

#### Hexadecimal

Hexadecimal numbers can also be used in Stof. A literal defined as a hex value is prefixed with "`0x`", followed by valid hexadecimal values (0-9, a-f, A-F). The [Number](../reference/libraries/number-library.md) library offers a way to parse strings to numbers as the Stof parser does at runtime.

{% hint style="info" %}
For readability, hexadecimal values can also contain "\_" characters. The [Number Library](../reference/libraries/number-library.md) parse functions take all of this into consideration as well.
{% endhint %}

```rust
fn example() {
    let hex = 0xFf;
    assertEq(hex, 255);
    
    // Number.parse and Number.parseHex come in handy
    hex = Number.parse("0xff");  // 255
    hex = Number.parseHex("ff"); // 255 - optional 0x
    assertEq(hex.toHexString(), "FF"); // always caps
}
```

#### Binary

Binary numbers can also be used in Stof. A literal defined as a binary value is prefixed with `"0b"`, followed by valid binary characters (1, 0).

{% hint style="info" %}
For readability, binary values can also contain "\_" characters. The [Number Library](../reference/libraries/number-library.md) parse functions take all of this into consideration as well.
{% endhint %}

```rust
fn example() {
    let bin = 0b0011;
    assertEq(bin, 3);
    
    // Number.parse and Number.parseBin come in handy
    bin = Number.parse("0b00_11"); // 3
    bin = Number.parseBin("11");   // 3
    assertEq(bin.toBinString(), "11");
}
```

#### Octal

Octal numbers can also be used in Stof. A literal defined as an octal number is prefixed with "`0o`", followed by valid octal characters (0-7).

{% hint style="info" %}
For readability, octal values can also contain "\_" characters. The [Number Library](../reference/libraries/number-library.md) parse functions take all of this into consideration as well.
{% endhint %}

```rust
fn example() {
    let oct = 0o55;
    assertEq(oct, 45);
    
    // Number.parse and Number.parseOct come in handy
    oct = Number.parse("0o55");  // 45
    oct = Number.parseOct("55"); // 45
    assertEq(bin.toOctString(), "55");
}
```

### Floating-point Numbers

A floating-point number is a number with decimal points. Stof has one floating-point type, which is a "float". This is implemented as an f64, which on modern CPUs is roughly the same speed as a 32-bit floating-point number with more precision.

{% hint style="info" %}
For added readability, floating-point numbers can be prefixed with either a "+" or "-" and can contain as many "\_" characters as you wish. For example, `+1_000_000.32_32` will equal `1000000.3232`. Scientific notation is also supported: `+1_000e3` will equal `1000000`.
{% endhint %}

```rust
float field: 42.0; // f64 field named "field" - "float" is optional here

fn example(): float {
    return 42.0;   // f64
}
```

### Operations

Stof supports the basic operations for number types: addition, subtraction, multiplication, division, and remainder.

```rust
#[test(42.0)]
fn addition(): float {
    return 12 + 30.0;
}

#[test(10)]
fn subtraction(): int {
    return 20 - 10;
}

#[test(100)]
fn multiplication(): int {
    return 10 * 10;
}

#[test(5)]
fn division(): int {
    return 10 / 2;
}

#[test(2.0)]
fn remainder(): float {
    return 12.0 % 10.0;
}
```

Stof also supports bitwise operations for number types: and, or, xor, shift left, and shift right.

```rust
#[test]
fn and() {
    let i = 0b0011 & 0b1001;
    assertEq(i, 0b0001);
}

#[test]
fn or() {
    let i = 0b0011 | 0b1001;
    assertEq(i, 0b1011);
}

#[test]
fn xor() {
    let i = 0b0011 ^ 0b1001;
    assertEq(i, 0b1010);
}

#[test]
fn shift_left() {
    let i = 0b0011 << 2;
    assertEq(i, 0b1100);
}

#[test]
fn shift_right() {
    let i = 0b1100 >> 3;
    assertEq(i, 0b0001);
}
```

### Units

A variant of a floating-point number exists in Stof with units attached. Stof performs unit conversions when you cast a units scalar to a different units type. For comparisons, Stof finds a common base unit to compare with. For operations, Stof will convert differing units to the same units.

Take a look at the [units.md](units.md "mention") page for an overview of available units in Stof.

```rust
fn example() {
    let x = 2cm;    // 2.0 with units of centimeters
    let y: mm = 230; // 230.0 with units of millimeters
    
    let res = x + y; // 25.0 with units of centimeters
    let meters = res as meters; // 0.25 with units of meters
}
```

### Booleans

Booleans can have one of two possible values: true or false.

```rust
bool field: true  // Boolean field with the value "true" - "bool" is optional here

fn example(v: bool): bool {
    return !v;
}
```

### Strings

Strings are Stof's most primitive alphabetic type. String literals can be declared using double or single quotes, can be multi-line, and are very versatile. Stof also has template strings, which expand into a string using expressions and unions.

```rust
str field: "world"; // "str" is optional - can also be single quotes or a template

fn example(): str {
    let first = 'hello';
    return `${first} ${self.field}!`; // "hello world!"
}
```

### Blobs

Blobs are Uint8Arrays in JavaScript or Vec\<u8> in Rust. They represent a binary blob of data and can be converted to and from Stof vectors if needed. Stof has blobs because it is a nice catch-all for data that isn't in a form Stof understands yet or isn't necessary for Stof to work with. An example might be speech data in a talk-to-text LLM transaction. Stof in that case might just be the messenger.

Blobs are a scalar type because Stof treats blobs as a singular value, it is not a compound type that contains multiple Stof values.

Default casting between blobs and strings is UTF-8.

```rust
blob myblob: [0, 0, 0]; // Vec<u8> instead of a Stof vec

blob json_stream: '{"field": true}'; // UTF-8 Vec<u8>

fn to_json(): str { // return type is "str", so the "as str" is actually optional
    return self.json_stream as str; // '{"field": true}' - From UTF-8 to String
}
```

Another common scenario is in messaging systems, where data often travels as blobs. Stof can import blobs just like text through the document "header\_import", which takes a format and/or content type. More on this later, but a very practical use of the blob type.

## Compound Types

Compound types group multiple values into one type. Stof has four compound types: tuples, vectors, maps, and sets. In other languages or formats, vectors are called arrays or lists. Vectors in Stof are not typed and, therefore are compatible with JSON (and other formats) arrays. The same goes for maps and sets.

### Tuples

A tuple is a general way of grouping multiple values of different types into a single compound type. Tuples are fixed in length and cannot grow or shrink once declared. They can, however, be cast to vectors for manipulation (values are copied).

```rust
(float, int, bool) field: (12.0, 10, false); // "(float, int, bool)" is optional

fn example(): (float, int, bool) {
    return (12.0, 10, true);
}
```

### Vectors

Vectors, unlike tuples, are able to grow and shrink in size. Unlike many other languages, vectors in Stof can contain values of many different types.

```rust
vec field: [42, 'hi', true, { embedded: true }];

fn example(): vec {
    return 0..5; // [0, 1, 2, 3, 4]
}
```

### Sets

Sets are similar to vectors, however, are always ordered in Stof, and are more efficient for performing contains checks, unions, intersections, etc...

```rust
fn example(): set {
    let set = set(1, 2, 3, 4, 5);
    assert(set.contains(3));
    return set;
}
```

### Maps

Just like in other languages, maps are useful for associating keys with values. Maps in Stof are ordered just like sets and accept any Stof type as key or value.

```rust
fn example(): map {
    let mp = map([("a", "A"), ("b", "B")]);
    assertEq(mp.get("a"), "A");
    return mp;
}
```

## Box Types

By default, every value in Stof is passed around by value, meaning values are copied often. In a lot of situations, this is just fine. To pass values by reference, however, Stof also offers the Box type, which wraps any other Stof value and places it on the heap.

A boxed value will be passed by reference and can be created with a cast or the standard library "box" function. Boxed values in Stof dereference themselves when needed, for example here in the equality check.

```rust
fn addOne(val: Box<int>) {
    val += 1;
}

#[test]
fn example() {
    // let val: Box<int> = 1;    // also works
    // let val = 1 as Box<int>;  // also works
    
    let val = box(1);            // will only box if not already boxed
    self.addOne(val);
    assertEq(val, 2);
}
```

For scalar types, boxing doesn't always make sense. However, for compound types, it often does when they are passed between functions. Or, when you'd like to manipulate values within compound types.

```rust
#[test]
fn example() {
    let vec = [box(0), box(1), box(2), box(3)];
    for (val in vec) {
        val += 1;
    }
    assertEq(vec, [1, 2, 3, 4]);
}
```

```rust
fn addItem(dict: Box<map>, key: unknown, value: unknown) {
    dict.insert(key, value);
}

#[test]
fn example() {
    // NB neglecting the box() here will still run but as the function above casts
    // the map into a Box<map>, it will do so AFTER the map has been copied.
    // This is to keep "dict" as the type we expect (map instead of Box<map>).
    // Try this yourself - the assert below will fail and "dict" will be empty.
    let dict = box(map());
    
    self.addItem(dict, 0, "a");
    self.addItem(dict, 1, "b");
    self.addItem(dict, -3, "c");
    
    assertEq(dict.values(), ["c", "a", "b"]); // maps are ordered
}
```

## Unknown Type

Every value in Stof always has a type, however, there are certain times (especially with imported data) that you want to accept a type into a value or function but aren't sure what it is. This is where the "unknown" type comes in handy.

{% hint style="info" %}
The "unknown" type is just syntactic sugar that accepts any value type.
{% endhint %}

```rust
fn idontknow(value: unknown) {
    pln(typeof value);
}

#[main]
fn example() {
    self.idontknow(box(12));
    self.idontknow("hello");
    self.idontknow(("hi", true, 12.1));
}
```

```
> stof run example.stof
Box<int>
str
(str, bool, float)
```

## Data Type

Because Stof is primarily a data organization strategy and structure, it offers a "data" type as an opaque reference to any data type (including functions and fields).

This type allows a functional interface to unknown, complex types through custom libraries. Instead of loading data into fields, this allows one to work with larger and more complex data types.

{% hint style="info" %}
Take a look at [introduction-and-design.md](../book/introduction-and-design.md "mention") for more information on how Stof works and the [Data Library](../reference/libraries/data-library.md) for more information on how to use this value type.
{% endhint %}

```rust
field: 42

fn pass_data(dref: data): bool {
    return dref.exists();
}

#[test]
fn data_type() {
    // not the contents of the field, rather a pointer to the field data itself
    let dref: data = Data.from('self.field');
    assert(self.pass_data(dref));

    let new_data = Data.fromId(dref.id());
    assert(self.pass_data(new_data));
}
```
