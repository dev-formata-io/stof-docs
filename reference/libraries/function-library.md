---
description: Stof's standard function library ("Function").
icon: function
---

# Function Library

## Common Value Functions

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.toString</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">str</mark>

Returns a string with this function's ID, helpful at times for debugging.

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.or</mark>(func: <mark style="color:green;">fn</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let func = self.dne.or((): str => "hey");
    assertEq(func.call(), "hey");
}
```

## Library Functions

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.call</mark>(func: <mark style="color:green;">fn</mark>, ...): <mark style="color:green;">unknown</mark>

Call this function, passing in any arguments given, in order.

```rust
fn add(x: float, y: float): float { return x + y; }

#[test]
fn test() {
    let func = self.add;
    assertEq(func.call(2, 2), 4);
    assertEq(Function.call(func, 10, 10), 20);
    
    func = (name: str): str => `Hello, ${name}!`;
    assertEq(func.call("Melissa"), "Hello, Melissa!");
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.name</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">str</mark>

Return the name of this function.

```rust
fn add(x: int, y: int): int { return x + y; }

#[test]
fn test() {
    assertEq(self.add.name(), "add");
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.parameters</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">vec</mark>

Returns a vector containing this function's parameters (a Tuple of `(name: str, type: str)`).

```rust
fn add(x: int, y: int): int { return x + y; }

#[test]
fn test() {
    assertEq(self.add.parameters(), [("x", "int"), ("y", "int")]);
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.returnType</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">str</mark>

Returns this function's return type (typeof).

```rust
fn add(x: int, y: int): int { return x + y; }

#[test]
fn test() {
    assertEq(self.add.returnType(), "int");
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.attributes</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">map</mark>

Returns a map of the attributes that exist on this function.

```rust
#[custom(2)]
#[test]
#[main]
fn main() {
    let attrs = self.main.attributes();
    assertEq(attrs.keys(), ["custom", "main", "test"]);
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.hasAttribute</mark>(func: <mark style="color:green;">fn</mark>, key: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Returns true if this function has an attribute with the given key.

```rust
#[custom(2)]
#[test]
#[main]
fn main() {
    assert(self.main.hasAttribute("test"));
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.object</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">obj</mark>

Return the first object that this function is attached to.

```rust
fn mul(a: float, b: float): float { return a * b; }

#[test]
fn test() {
    assertEq(self.mul.object(), root);
}
```

### <mark style="color:purple;">Function</mark><mark style="color:orange;">.objects</mark>(func: <mark style="color:green;">fn</mark>): <mark style="color:green;">vec</mark>

Return a vector of all of the objects that this function is attached to.

```rust
a: {
    fn hello(): str { return "hello"; }
}
b: {
    ref root.a.hello;  // syntax for Object.reference(obj, path: str)
}

#[test]
fn test() {
    assertEq(self.a.hello.objects().len(), 2);
    assertEq(self.a.hello(), "hello");
    assertEq(self.b.hello(), "hello");
}
```
