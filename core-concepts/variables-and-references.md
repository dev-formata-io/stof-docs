---
description: Stof variables.
---

# Variables & References

Variables in Stof allow data to live in the runtime temporarily (outside of the document) while it is being transformed or utilized.

Defining a variable is done with either the "let" or "const" keywords. Const variables cannot be reassigned once defined.

Variables can also be defined with a type `let value: float = 5;` - value is cast to a "float" if needed, and will continue casting values to "float" when they are assigned (maintains the type "float").

```rust
start: "Hello, "
end: "!!"

#[main]
fn main() {
    let start = self.start;
    const end = self.end;
    const name: str = "John";
    pln(start + name + end);
}
```

## Scopes

Variables are organized into scopes, like in other languages. Scopes get created anytime a block is started with a beginning brace (if statements, loops, switch statements, block expressions, etc.).

```rust
#[main]
fn main() {
    let outer = 10;
    
    {
        let inner = 10;
        pln(outer + inner); // can use outer scopes
    }                       // inner scope is deleted
    
    // pln(outer + inner); // error - inner scope no longer exists
}
```

## Value Type vs Reference Type

Like a lot of languages, Stof has a distinction between a value type (auto copy by value) and a reference type (auto copy by reference).

### Value Types

Booleans, numbers, strings, versions, and promises are value types, which get automatically copied by value when passed around.

```rust
value: 42

#[main]
fn main() {
    let val = self.value; // int copied by value here
    val += 50;
    assert_eq(val, 92);
    assert_eq(self.value, 42);
}
```

With the reference operator "&", you can work with the value by reference instead, but for value types, this has to be done by you.

```rust
value: 42

#[main]
fn main() {
    let val = &self.value; // & means the value should be copied by reference
    val += 50;
    assert_eq(val, 92);
    assert_eq(self.value, 92);
}
```

See if you can follow the given example, then try it yourself:

```rust
fn push_name(string: str, name: str) {
    string.push(name);
}

#[main]
fn by_value() {
    let string = "Hello, ";
    self.push_name(string, "John");
    pln(string);
}

#[main]
fn by_ref() {
    let string = "Hello, ";
    self.push_name(&string, "John");
    pln(string);
}
```

### Reference Types

Tuples, maps, sets, lists, and blobs are reference types. These values get copied by reference automatically.

{% hint style="info" %}
Objects, data pointers, and fn pointers are value types, technically, but they behave as reference types because they merely point into the Stof document (nodes and data components).

Because of this, they are cheap and easy to work with in collections, functions, etc.
{% endhint %}

```rust
value: [1, 2]

#[main]
fn main() {
    self.value.push_back(3, 4); // self.value is automatically copied by reference
    assert_eq(self.value, [1, 2, 3, 4]);
}
```

## Call by Reference

Some standard library functions support references as well, like the "at" operator (indexing).

```rust
#[main]
fn main() {
    const list = [1, 1, 1, 1];
    
    let val = &list[1]; // this is the same as "&list.at(1)"
    val += 99;
    
    let last = &list.back();
    last += 99;
    
    assert_eq(list, [1, 100, 1, 100]);
}
```

## Loop by Reference

The "for in" loop can also create variables by reference, since it is calling "at" behind the scenes.

```rust
#[main]
fn main() {
    const list = [1, 2, 3];
    for (let val in &list) val += 5;
    assert_eq(list, [6, 7, 8]);
}
```
