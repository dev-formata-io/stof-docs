---
description: An overview of Stof functions.
---

# Functions

Functions in Stof are no different than fields in many ways. They are attached to objects in the same way, can be dynamically added or removed, and are referenced the same way via paths.

Stof by default only has access to the data and resources it contains (fields, functions, libraries, and formats). To give Stof access to the system or the outside world, a library must be added to the document by the host system running Stof. Libraries and formats do not travel with Stof between systems, ever.

## Basic Example

```rust
/// Add two floating-point numbers together
fn add(a: float, b: float): float {
    return a + b;
}

#[main]
fn main() {
    let x = self.add(10, 33.3);
    assertEq(x, 43.3);
}
```

In this Stof document, we have two functions attached to the "root" object. When we run the document, all functions with the attribute "main" will be called, hence the "#\[main]" above the function also named "main". Documents can have multiple main functions. We could also call the main function directly by path, referencing its name instead of the attribute. More on this later.

Functions have (optional) attributes, a name, (optional) parameters, and an (optional) return type, just like many popular programming languages.&#x20;

Unlike other programming languages, Stof will cast values to the types expected for you.

## Value Type Casting

```rust
/// Parameters of any type will be cast to 'str' here
fn combine(a: str, b: str): str {
    return a + b;
}

#[main]
fn main() {
    let res = self.combine(true, 200);
    assertEq(res, "true200");
}
```

```rust
/// Return types perform casting as well if needed
fn kilometers(a: float): km {
    return a;
}

#[main]
fn main() {
    let kms = self.kilometers(22); // int literal cast to float, then cast to km
    assertEq(kms.units(), 'km');
}
```

## Paths

Functions are referenced via paths just like objects and fields. See [objects.md](objects.md "mention") to read more on paths and how they work.

```rust
child: {
    fn add(a: int, b: int): int {
        return a + b;
    }
}

sibling: {
    #[main]
    fn main() {
        let res = super.child.add(12, 12);
        assertEq(res, 24);
    }
}
```

## Attributes

Just like fields, functions can have attributes associated with them.

### Syntax

```rust
#[attribute('optional expression')]
#[attribute2] // expression value of null
#[attribute3] // as many attributes as you'd like/need
fn function() {
    // do stuff
}
```

### Main Attribute

The main attribute marks functions that should be invoked when the document is ran. Alternatively, one can call specific functions in a document.

There is no limit to the number of main functions a document can have, nor a restriction on the objects that can contain a main function(s).

```rust
#[main]
fn shouldRun() {
    // Will get called on doc run
}

#[main(42)]
fn parameters(val: int) {
    // "val" will be 42 when the document is ran
    // Otherwise, use this function as normal, passing any int in as "val"
}
```

### Field Attribute

The "field" attribute on a function tells Stof to also create a field with the same name on the object the function is defined that points to the function.

The field attribute helps in scenarios where a field can be either a value or a function. A machine or person parsing an object for a field, can see the field, even if it is declared as a function. An alternative is to just use an arrow function when creating the field. However, then, the function behind it will have a mangled name.

```rust
#[field] // creates the field "name" with a "fn" value pointing to this function
fn name(): str {
    return 'bob';
}
```

```rust
// Arrow function alternative to the #[field] attribute
// Created function has an anonymous name
name: (): str => 'bob';
```

```rust
// Verbose version
// Function named "name"
fn name(): str {
    return 'bob';
}

// Field named "name" pointing to the function already defined
name: self.name;

#[test]
fn test() {
    // You might think we have an issue with names, but they are different data types
    // This will work. In general, though, Stof looks for fields, then functions
    assertEq(self.name.call(), 'bob');
    assertEq(self.name(), 'bob');
}
```

### Test Attribute

Stof documents have a built-in "test" attribute for creating and running tests.

```rust
#[test]
fn someTest() {
    // This function will get called when a document gets tested
    // Assert functions are a part of the std lib, so a good place to call them
}

#[test]
#[errors] // Attribute to show that the test should throw an error/fail an assert
fn testThatThrows() {
    throw("This is an error");
}

#[test('hello')] // Only passes if the return value equals the given expression
fn testReturnEquality(): str {
    return 'hello';
}

field: 42;
#[test(self.field)] // These expressions get evaluated when the document is parsed
fn actualExpressions(): int {
    return 42;
}

#[test]
#[silent] // Not commonly needed/used
fn dontPrintOutput() {
    // This function when tested will not print ok/fail message to the console
    // Example use is when panics are disabled and a failure is expected
}
```

### Init Attribute

Stof has a built-in "init" attribute, indicating the functions that should run when the document is parsed. Every "init" function gets called AFTER the document has been parsed, with the purpose of allowing for manipulating the data before a user calls additional functions or accesses fields.

```rust
#[init]
fn create_data() {
    // This function will be called when this document is parsed, at the end,
    // creating a new field on this object called "initialized" with a value "true".
    self.initialized = true;
}
```

## Arrow Functions

```rust
// A field with a type of "fn", pointing to an anonymous function in the document
func_field: (): float => {
    return 42.0;
}

#[test]
fn call_field() {
    assertEq(self.func_field.call(), 42.0);         // points to the Function lib automatically
    assertEq(Function.call(self.func_field), 42.0); // using the Function lib explicitly
}
```

```rust
#[test]
fn terrible() {
    let result = ((): str => "hello").call();
    assertEq(result, 'hello');
}

#[test]
fn better() {
    let func = (): str => {
        return 'hello';
    };
    
    let result = func.call();
    assertEq(result, "hello");
}
```

## Functions as Values

```rust
fn callFunc(func: fn) {
    func.call('John');
}

fn sayHi(name: str) {
    pln(`Hello, ${name}!`); // Std lib for printing a line to the console
}

#[test]
fn test() {
    self.callFunc(self.sayHi); // Prints 'Hello, John!'
}
```

## Function Decorator

```rust
int calls: 0
fn dec(func: fn): fn {
    return (a: float, b: float): float => {
        self.calls += 1;
        return func.call(a, b);
    };
}

#[decorator(self.dec)]
fn add(a: float, b: float): float {
    return a + b;
}

#[test]
fn add_decorator() {
    let res = self.add(3, 4);
    assertEq(res, 7);
    assertEq(self.calls, 1);

    res = self.add(10, 10);
    assertEq(res, 20);
    assertEq(self.calls, 2);
}
```
