---
description: Stof type system.
---

# Types

There are two primitive data types: scalar and compound. Stof also has an "unknown" type, union types, a "data" or "Data\<My Data>" type for custom or complex data, a semantic version type, and a prototype system for objects.

Types add consistency and stability to your code in such a dynamic environment. For times when you need dynamic capabilities, "unknown" matches any type and allows you to type-check data yourself.

For fields and variable declarations, types are implied by value. However, if you define a type, the field or variable will maintain that type, casting values to that type when assigned.

## Scalar Types

A scalar type represents a single value. Stof has 5 primary scalar types: integers, floating-point numbers, booleans, strings, and blobs.

Stof also has functions, data pointers, objects, semantic versions, and promises as types too, but we'll see those later on.

### Integers

An integer is a number without a fractional component. Stof has one integer type, which is an "int". This is a signed 64-bit integer.

```rust
int field: 42

#[main]
fn main() {
    let var: int = -42;
    const val = +1_000_000; // readability
    
    let hex = 0xFf;   // int hex = 255;
    let bin = 0b0011; // int bin = 3;
    let oct = 0o55;   // int oct = 45;
}
```

### Floating-point Numbers

A floating-point number is a number with a fractional part. Stof has one floating-point type, which is a "float" - a 64-bit signed floating-point number (f64).&#x20;

```rust
float field: 42.0

#[main]
fn main() {
    const var: float = -42.0;
    let val = +10_000.54;
}
```

### Booleans

Booleans can have one of two possible values: true or false.

```rust
bool field: true

#[main]
fn main() {
    let val = !true;
    assert_eq(val, false);
}
```

### Strings

String literals can be declared using either double or single quotes. Or with a raw string syntax `r#".."#`, which accepts multi-lines and does not escape characters.

```rust
#[main]
fn main() {
    let literal = "\nHello, world\n";
    assert_eq(literal, '\nHello, world\n');
    assert_eq(literal, #r"
Hello, world
"#);
}
```

Strings can also be constructed with a template syntax:

```javascript
let literal = `Hello, ${self.name}`; // everything inside ${expr} is a Stof expression
```

### Blobs

A blob in Stof represents a Vec\<u8> (Rust) or Uint8Array (JS). For large amounts of data, this is a lot more efficient than a list.

Blobs are useful for representing abstract binary data or as an exchange between APIs or data types.

```rust
#[main]
fn main() {
    const bytes: blob = "hello"; // blob <-> str is utf-8
    assert_eq(bytes as str, "hello");
}
```

## Compound Types

Compound types group multiple values into a single type. Stof has four compound types: tuples, lists, maps, and sets.

### Tuples

A general way of grouping types into a singular value.

```rust
#[main]
fn main() {
    let tuple: (int, str, bool) = (42, "hi", true);
    assert_eq(tuple[0], 42);
    assert_eq(tuple.len(), 3);
    
    ((tup: (int, str, bool)) => pln(tup))(tuple);
}
```

### Lists

A list can grow and shrink in size, is unordered, and can be manipulated at both the front and back.

```rust
#[main]
fn main() {
    let list: list = ["hello", true, 42];
    list.push_back(100);
    list.push_front(50);
    assert_eq(list, [50, "hello", true, 42, 100]);
    
    for (const val in list) { /* do something with each value */ }
    
    ((l: list) => pln(l))(list); // type is "list", always by reference
}
```

### Sets

A set can change in size, is ordered, and can not contain duplicate values.

```rust
#[main]
fn main() {
    let set: set = {1, 3, 4, 2, 2, 3};
    assert_eq(set, {1, 2, 3, 4});
    assert(set.contains(4));
    
    ((s: set) => pln(s))(set); // type is "set", always by reference
}
```

### Maps

Maps can also change in size, are ordered, and cannot contain duplicate keys.

```rust
#[main]
fn main() {
    let map: map = {"a": "A", "b": "B"};
    assert(map.contains("a"));
}
```

## Semantic Version

Versioning is an important aspect of APIs, and because Stof is so useful for defining, combining, and interfacing with APIs, it has a built-in version type. See [Semantic Versioning](https://semver.org/) for more information on versions.

```rust
#[main]
fn main() {
    let version: ver = 1.2.3-release+build; // release and build are optional
    
    const my_version = 0.8.0;
    const other_version = 0.7.11;
    assert(my_version > other_version);
    assert(my_version.patch() < other_version.patch());
}
```

## Unknown

The "unknown" type is difficult to accept as a language designer, but very needed in Stof, where languages, data, and APIs come together dynamically. Especially useful in use cases like schemas, dynamic field transformations, or when you're not sure what type of data you'll receive in a function call.

```rust
#[main]
fn main() {
    const func = (v: unknown): unknown => v; // takes and returns any value type
    assert_eq(func("hi"), "hi");
    assert_eq(func.call(42), 42);
}
```

## Union Types

Union types are generally preferred to the "unknown" type, acting as an OR filter for matching types.

```rust
#[main]
fn main() {
    const func = (v: int | str): int | str => v;
    assert_eq(func("hi"), "hi");
    assert_eq(Fn.call(func, 32), 32);
}
```

## Data

As outlined in the [introduction-and-design.md](../introduction-and-design.md "mention"), Stof organizes a lot of different types of data, even custom types. The "data" type is an opaque pointer to any data that exists on a node (object). This includes functions and fields, but also data that you could define yourself, like a PDF document, Image, or anything you'd like.

{% hint style="info" %}
We'll go over the data concept in greater detail later on in the book.
{% endhint %}

```rust
field: "hello"

#[main]
fn main() {
    const field_data: data = Data.field("self.field");
    assert_eq(self.field, "hello");
    field_data.drop(); // easier to use Std.drop('self.field') though..
    assert_eq(self.field, null);
}
```

### Complex Data

The "data" type matches all data; however, Stof has a "Data<..>" syntax for matching specific types of data, even custom types that you add yourself.

This is nice, because you can have large data types as multiple fields, passed around between functions, etc., with no overhead.

{% hint style="info" %}
"data" matches the type "Data\<Pdf>", but "Data\<Pdf>" will not match the type "Data\<Image>". Think of these complex data types as a subtype or specific type of "data".
{% endhint %}

```rust
fn do_pdf_things(pdf: Data<Pdf>) {
    // pdf is a data pointer that references the subtype and library "Pdf"
    const text = pdf.extract_text(); // get all text from the Pdf
    assert_eq(Data.libname(pdf), "Pdf");
    assert_eq(typename pdf, "Data<Pdf>");
    assert_eq(typeof pdf, "data");
}
```

## Prototypes

Objects in Stof can be used as prototypes for other objects. There is a special #\[type] attribute for objects that, when the parser sees it, it will create a typename link to that object.

{% hint style="info" %}
We'll go over prototypes in greater detail later in the book.
{% endhint %}

```rust
#[type]
Point: {       // normal obj syntax other than the #[type] attribute
    float x: 0 // will be initialized with this value if non-existent
    float y: 0
    float z: 0
    
    fn length() -> float {
        Num.sqrt(self.x.pow(2) + self.y.pow(2) + self.z.pow(2))
    }
}

#[main]
fn main() {
    // "new" syntax will create a new object in this document
    const point = new Point { x: 2, y: 2, z: 2 };
    assert_eq(point.length().round(2), 3.46);
}
```
