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

#### Unit Types

Stof also has a variant of a floating-point number for units. Units can act as types on their own and are outlined in greater detail on the [numbers.md](numbers.md "mention") page.

For example, the function `Time.now()` in the [time-library-time.md](../../libraries/time-library-time.md "mention"), returns a floating-point number with units of "ms". The unit "ms" can be used in place of the type "float" as a more specific type of number, enabling conversions when the number is cast.

{% hint style="info" %}
All unit types match the type "float" (Ex. function parameter).
{% endhint %}

```rust
#[main]
fn main() {
    const last_week_now: hours = Time.now() - 7days; // units are so nice...
    
    // do something with timestamp in hours since Unix Epoch...
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
    
    // syntax for blob initialization (not a list)
    const msg = |104, 101, 108, 108, 111, 44, 32, 119, 111, 114, 108, 100|;
    assert_eq(msg as str, 'hello, world');
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

The "unknown" type is needed in Stof, where languages, data, and APIs come together dynamically. Especially useful in use cases like schemas, dynamic field transformations, or when you're not sure what type of data you'll receive in a function call.

{% hint style="info" %}
The "unknown" type is just syntax for "match with any type". Each value always has a type, so when using "unknown" (or union types), make sure to check types yourself to ensure operations do not result in an error.
{% endhint %}

```rust
#[main]
fn main() {
    // takes and returns any type of value
    const func = (v: unknown): unknown => {
        if ("str" == typeof v) return v + "!!";
        v
    };
    assert_eq(func("hi"), "hi!!");
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

## Promises

Stof supports asynchronous data manipulation and, therefore, has a promise type. A promise holds a reference to another Stof process, and when awaited, will wait for that process to finish executing before returning its result.

{% hint style="info" %}
The promise type matches its inner type, and "await" is a passthrough operation unless the value is a promise. This means you rarely need to write out the "Promise\<inner>" type - you can just use "inner" in its place.
{% endhint %}

```rust
async fn async_function() -> Promise<str> {
    "hello, promises"
}
async fn without_promise() -> str { // same result as above
    "hello, looks"
}

// recommended over just "str" if you're expecting promises as arguments
fn takes_promise(arg: Promise<str>) -> str {
    await arg
}

#[main]
fn main() {
    const promise = self.async_function();
    assert_eq(typeof promise, "Promise<str>");
    assert_eq(await promise, "hello, promises");
    
    const without = self.without_promise();
    assert_eq(typeof without, "Promise<str>");
    assert_eq(self.takes_promise(without), "hello, looks");
    
    // str == Promise<str> for type matching
    // await only does something when given a promise value
    assert_eq(self.takes_promise("woah.."), "woah..");
}
```

## Data

As outlined in the [design.md](../design.md "mention"), Stof organizes a lot of different types of data, even custom types. The "data" type is an opaque pointer to any data that exists on a node (object). This includes functions and fields, but also data that you could define yourself, like a PDF document, Image, or anything you'd like.

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

## Objects

Objects in Stof have an "obj" type. Behind the scenes, they reference nodes and can contain much more diverse data types than just fields and functions.

{% hint style="info" %}
The map constructor syntax {"key": "value"} looks like the object syntax, but they are two completely different types.
{% endhint %}

```rust
obj object_field: {
    // this is a new object that can contain objects, fields, data, and functions
    // both this field and object have the name "object_field"
}

#[main]
fn main() {
    const object: obj = new {
        // this is the syntax for creating a new object within the document
        // the "new" keyword should stand out for creating a new document node
        // unless dropped (Std.drop(object)), this will increase the doc size
    };
    
    // for comparison, this the map initialization syntax
    const map: map = {'a': 1, 'b': 2};
}
```

## Prototypes

Objects in Stof can be used as prototypes for other objects. There is a special #\[type] attribute for objects that, when the parser sees it, it will create a typename link to that object.

{% hint style="info" %}
Take a look at [prototypes.md](prototypes.md "mention") for more information.
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
    
    // typeof will always be the general "obj" type
    // typename is always the more specific type
    assert_eq(typeof point, "obj");
    assert_eq(typename point, "Point");
}
```

## Null

Yup, Stof has a null value type. So many data formats and languages have null, that for interoperability, it was unavoidable.

```rust
int value: null // value of "null" and can be assigned with an int or null only

fn takes_value(val: str) {
    pln(val);
}

#[main]
fn main() {
    // can pass null values
    self.takes_value(null);
}
```

### Not Null Types!

There is a way to combat this, though - ensuring an error is thrown when a null value is given where it shouldn't be.

The answer is a "not null" type operator :tada:! Not a perfect solution, but it helps a lot.

{% hint style="info" %}
The `!` postfix type operator works with all types, anywhere. So use with object prototype names, units, etc.!

Also handy to use this on a return type, ensuring a function does not return null.
{% endhint %}

```rust
int! value: null // this will now throw an error, can only be an int!

fn takes_value(val: str!) {
    pln(val);
}

#[main]
fn main() {
    // passing the correct value or a castable value is fine
    self.takes_value(42); // gets cast to "42"

    // passing null will result in an error
    self.takes_value(null);
}
```
