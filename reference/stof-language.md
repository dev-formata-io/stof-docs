---
description: Stof language reference sheet.
icon: code
---

# Stof Language

Stof is designed to be very easy to use. It is not a general-purpose language; it only exists as a scalable, secure, and portable interface to data and remote systems (APIs).&#x20;

In general, Stof should be considered a document rather than a program. One of its main goals is to be a medium for different data types to come together (data unification). The language is only one of these data types, acting as a controlled interface to the others.

## Types&#x20;

{% hint style="info" %}
See [primitive-types.md](../common-concepts/primitive-types.md "mention") and [units.md](../common-concepts/units.md "mention") for more information on types in Stof.
{% endhint %}

| Stof Type (keyword)                                                                                                                                                    | Description                                                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| <mark style="color:purple;">obj</mark>                                                                                                                                 | Object                                                                  |
| <mark style="color:purple;">null</mark>                                                                                                                                | Null value                                                              |
| <mark style="color:purple;">str</mark>                                                                                                                                 | String                                                                  |
| <mark style="color:purple;">int</mark>                                                                                                                                 | Integer (i64)                                                           |
| <mark style="color:purple;">float</mark>                                                                                                                               | Floating point number (f64)                                             |
| Units (<mark style="color:purple;">m</mark>, <mark style="color:purple;">km</mark>, <mark style="color:purple;">s</mark>, <mark style="color:purple;">ms</mark>, etc.) | [units.md](../common-concepts/units.md "mention") are f64 variant types |
| <mark style="color:purple;">bool</mark>                                                                                                                                | Boolean (true or false)                                                 |
| <mark style="color:purple;">map</mark>                                                                                                                                 | Maps keys to values (ordered)                                           |
| <mark style="color:purple;">set</mark>                                                                                                                                 | Set of values (ordered)                                                 |
| <mark style="color:purple;">blob</mark>                                                                                                                                | Binary array of values (Uint8Array or Vec\<u8>)                         |
| <mark style="color:purple;">vec</mark>                                                                                                                                 | Vector/Array of values                                                  |
| <mark style="color:purple;">fn</mark>                                                                                                                                  | Function type                                                           |
| <mark style="color:purple;">unknown</mark>                                                                                                                             | Unknown type (used to accept many types)                                |
| <mark style="color:purple;">void</mark>                                                                                                                                | Allowed on functions that don't return a value                          |

## Comments

### Line Comment

```rust
// This is a line comment in Stof
```

### Block Comment

```rust
/*
  This is a block comment in Stof.
 */
```

## Fields

{% hint style="info" %}
For declaring fields, Stof is a superset of JSON, valid JSON will always be valid Stof.
{% endhint %}

```json
{ // outer braces are optional in Stof
    "message": "Stof can parse the JSON format!"
}
```

```
<attributes>*
<type>? <name>: <object | array | expr> <"," | ";">?
```

Given this syntax, the above "message" field could be written as any one of the following:

```
"message": "value"     // variation 1
'message': 'value'     // variation 2
message: "value"       // variation 3
str message: 'value'   // variation 4
str message: "value",  // variation 5
str message: 'value';  // variation 6
```

If the value were an object, the field could be written as:

```
field: {
    // additional fields in this object
}

// OR
obj field: {},

// OR
obj field: {};
```

### Value Expressions

Fields get parsed from the top of the document downwards, executing each value expression as they are seen.

```rust
fn hello(): str { return "hello"; }

str field: self.hello() // creates a field with the value "hello" ("str" optional)
```

If we reversed the order of the function declaration and field declaration in this example, the document would fail to parse, resulting in an error.

However, if a function is parsed that references a field that has not yet been parsed, this is fine as long as it isn't called until after the document is parsed (or all fields/functions it references).

This functionality can help improve the readability of your documents as well:

```rust
cm height: 6ft + 1in; // casts 6ft + 1in to centemeters when parsed
```

#### Block Expressions

Stof also has block expressions, which can be used when defining fields (or within functions of course).

```rust
field: { // This isn't an object - Stof will execute while parsing
    let val = 12 + 100;
    return val;
};

#[test]
fn test() {
    assertEq(self.field, 112);
}
```

### Objects

Objects are defined as fields, but start a new scope that can contain additional fields, functions, and types (along with other data if you choose).

{% hint style="info" %}
See [objects.md](../common-concepts/objects.md "mention") for more information on objects in Stof.
{% endhint %}

```rust
obj myobject: { // remember, "obj" type here is optional
    str field: "hello, there"
}
```

### Arrays

Array fields are defined in a familiar way.

```rust
vec array: [ // just like JSON, and the "vec" type is optional
    {
        // can have fields, functions, types, etc... no different than outer objects
    } as obj, // optional syntax for casting objects to complex types from an array
    true,
    "hi",
    32
]
```

## Functions

{% hint style="info" %}
See [functions.md](../common-concepts/functions.md "mention") for more information on functions in Stof. This is merely a syntax reference.
{% endhint %}

Functions in Stof should feel pretty familiar, following the general syntax:

```
<attributes>*
fn <name>(<params>?): <return_type>? { <statements> }
```

### Parameters

```
<name>: <type> = <expr>?
```

Parameters are optional, consisting of a name, type, and an optional default value.

```rust
fn noParams() {}
fn oneParam(val: str) {}
fn defaultVal(val: str = "hello") {}
fn multipleParams(a: str, b: str, c: str) {}
```

### Return Type

```rust
fn noReturns() {}
fn returns(): str { return "hello"; }
```

### Attributes

```rust
#[attribute]
fn hasAttribute() {}

fn noAttributes() {}

#[a]          // attribute with a value of null
#[b("hello")] // attribute with a value of "hello"
fn multipleAttributes() {}
```

## Statements

Statements are used in functions and code blocks to manipulate the document, perform transformations, and execute custom logic.

### Variable Declaration

A variable is declared in Stof using the "let" keyword. Variables are not fields, but field values can be loaded into variables for mutation.

```rust
fn example() {
    let a = 32;       // declaration with implicit type
    let b: int = 43;  // declaration with explicit type (casts if needed)
}
```

### Assignment

Assignment is special in Stof as variables can be assigned to, sure, but fields in the document can be set this way also.

```rust
field: 30

fn example() {
    let val = self.field;
    val = val + 10;   // val is 40, field is still 30 - could also use "+=" here...
    self.field = val; // sets field to 40
}
```

{% hint style="warning" %}
Fields are not references, as seen in this example. This makes Stof much easier to use in real life, where undoing field manipulations on errors, etc. can be quite difficult or impossible.

It also improves the readability and maintainability of Stof, knowing exactly when and where fields are being set vs variables.

In some scenarios (like pushing to an array that is a field), this can be annoying. However, it's worth the minor inconvenience of another assignment.
{% endhint %}

#### Addition Assignment

Assignments to fields are allowed in all explicit assignment statements.

```rust
field: 30;

#[test]
fn example() {
    self.field += 20;
    assertEq(self.field, 50);
}
```

#### Subtraction Assignment

```rust
fn example() {
    let val = 10;
    val -= 5;
    assertEq(val, 5);
}
```

#### Multiplication Assignment

```rust
fn example() {
    let val = 2;
    val *= 2;
    assertEq(val, 4);
}
```

#### Division Assignment

```rust
fn example() {
    let val = 10;
    val /= 2;
    assertEq(val, 5);
}
```

#### Modulo Assignment

```rust
fn example() {
    let val = 3;
    val %= 4;
    assertEq(val, 3);
}
```

### Drop

The "drop" statement does exactly what it sounds like, frees variables. It can also delete fields, functions, and objects from the document entirely.

```rust
field: "hi"

#[test]
fn example() {
    let val = 20;
    drop val;
    assertNull(val);
    
    drop self.field;
    assertNull(self.field);
}
```

### If Statement

An "if" block is required, followed by optional "else if" blocks, and finally, an optional "else" block.

```rust
fn example() {
    let expr = true;
    let elif = false;
    
    if (expr) {
        // execute these statements if expr evaluates to a truthy value
    } else if (elif) { // can have multiple "else if" blocks
        // execute these statements if expr is falsy and elif is truthy
    } else {
        // execute these statements if expr is falsy and elif is falsy
    }
    
    if (true) pln("don't need braces either for a single statement...");
}
```

### Switch Statement

Switch statements evaluate an expression, matching the resulting value to a specified "case", or the "default" case if one is defined and a matching "case" was not found.

Cases in a switch statement do not fall through to the next case in Stof (like other languages) if a break or return is not found. Instead, Stof offers the "or" keyword to match multiple cases to a singular block of statements.

```typescript
fn example() {
    let val = "a";
    switch (val) {
        case "b": pln("doing b things");
        case "c": {
            // things related to c
        }
        case "d": {}, // optional comma to separate
        case "e": {}; // optional semi-colon to separate
        
        case "f" or
        case "g": {
            // handle multiple cases in one block...
        }
        
        default: {
            // optional default block to handle any value not matching a defined case
            pln("received: ", val);
        }
    }
}
```

### Try-Catch

Try-catch blocks are pretty simple in Stof, consisting of a singular try block and a singular catch block (no finally). Catch blocks are not required to, but can optionally catch the error type and message.

```
try-catch syntax:
    try { <statements> } catch <error>? { <statements> }

optional error syntax:
    (<id>: str)         // error message only
    (<id>: (str, str))  // (type, message)
    (<id>: map)         // id.type, id.message, id.stack (call stack functions)
```

{% hint style="info" %}
See [error-handling.md](../common-concepts/error-handling.md "mention") for more information on handling errors in Stof.
{% endhint %}

```typescript
fn errors(i: int) {
    if (i < 0) throw("you are wrong");
}

#[test]
fn handles() {
    try {
        self.errors(-34);
    } catch {
        pln("I was wrong");
    }
}

#[test]
#[errors] // tests to make sure this test throws an error
fn doesnt() {
    self.errors(-10);
}

#[test]
fn message() {
    try self.errors(-34);
    catch (message: str) {
        assertEq(message, 'you are wrong');
    }
}

#[test]
fn type() {
    try throw('CustomError', 'dude, this is not the right way');
    catch (error: (str, str)) {
        assertEq(error[0], 'CustomError');
        assertEq(error[1], 'dude, this is not the right way');
    }
}

#[test]
#[errors]
fn map() {
    try throw('MyError', 'not implemented');
    catch (error: map) {
        assertEq(error.get('message'), 'not implemented');
        assertEq(error.get('type'), 'MyError');

        // function stack to where the error occurred
        // last function in the stack is the one that threw an error
        assert(error.get('stack').len() > 0);

        // continues the error up the stack with an addition
        throw(error.get('type'), 'map error: ' + error.get('message'));
    }
}
```

### Return

```rust
fn emptyReturn(): void {
    return;
    pln("hello, there"); // never called...
}

fn returns(): str {
    return "hello"; // passes the value "hello" to the caller
    pln("dude"); // never called...
}
```

### While Loop

```rust
#[test([8, 6, 4, 2])] // asserts that the return value equals [8, 6, 4, 2]
fn example(): vec {
    let res = [];
    let val = 10;
    
    while (val > 0) {
        val -= 1;
        
        if (val % 2 == 0) {
            res.push(val);
            
            continue; // go directly to the next iteration of the loop
            pln("never gets printed");
        }
        
        if (val < 3) break; // exits the loop
    }
    return res;
}
```

{% hint style="info" %}
Just like in other programming languages, both the "break" and "continue" statements only apply to the most immediate loop (including for-loops).
{% endhint %}

### For Loop

```rust
fn example() {
    let res = [];
    for (let i = 0; i < 5; i += 1) res.push(i);
    assertEq(res, [0, 1, 2, 3, 4]);
}
```

{% hint style="info" %}
In real life, there's a range expression for constructing arrays of this nature:

\<start (inclusive)>..\<end (exclusive)>|\<step>?.

The above "res" array could be defined with the expression: `0..5`.
{% endhint %}

### For-in Loop

The for-in loop is the easiest and most common way to iterate over collections and objects in Stof. Any value that implements a "len" and "at" function can be iterated over using a for-in loop. This includes most of the value types in Stof by default: vectors, sets, maps, objects, tuples, blobs, numbers, and strings. Take a look at each [library](libraries/), the "at" function will tell you what type of value to expect.

{% hint style="info" %}
Each for-in loop defines 3 variables (in a separate scope) that commonly come in handy while iterating:

* first - true if inside the first iteration of the loop, false otherwise
* last - true if inside the last iteration of the loop, false otherwise
* index - the index of the current value
{% endhint %}

```rust
#[test([10, 1, 2, 3, 4, 30])]
fn example(): vec {
    let array = 10..34|4;
    let res = [];
    for (i in array) {
        if (first) res.push(i);      // first = true when i is the first value in the array
        else if (last) res.push(i);  // last = true when i is the last value in the array
        else res.push(index);        // i = array[index]
    }
    return res;
}
```

#### Custom Object Iteration

```rust
myobj: {
    fn at(index: int): unknown {
        return index * 12;  // default is to return the field value
    }
    fn len(): int {
        return 5;           // for objects, the default is the number of fields
    }
}
#[test([-100, 13, 26, 39, 100])]
fn customIteration(): vec {
    let res = [];
    for (val in self.myobj) {  // default val would be a tuple (key, value) of a field on myobj
        if (first) res.push(-100);
        else if (last) res.push(100);
        else res.push(val + index); // 12 + 1, 24 + 2, 36 + 3
    }
    return res;
}
```

## Expressions

In this section, we'll highlight some of the most handy and used expressions Stof has - this is not a complete list of expressions available.

### Function Calls

```rust
fn hello(): str { return "hello"; }

#[test]
fn example() {
    let val = self.hello(); // call is an expression, part of a declaration statement
}
```

### Tuple Construction

```rust
fn example(): (int, str) {
    return (32, "yo"); // expression that creates a tuple
}
```

### Array/Vector Construction

```rust
fn example(): vec {
    return [true, "hows it going?", 42];
}
```

### Index/At

```rust
fn example() {
    let vector = [3, 4, 5, 6];
    let val = vector[1]; // calls "at" - vector.at(1) is equivalent
}
```

### Range Constructor

```
<start (inclusive)>..<end (exclusive)> | <step>?
```

```rust
#[test]
fn basic_range() {
    let range = 0..5;
    assertEq(range, [0, 1, 2, 3, 4]);
}

#[test]
fn step_range() {
    let range = 4..11|3;
    assertEq(range, [4, 7, 10]);
}

#[test]
fn backwards() {
    let range = 24..19;
    assertEq(range, [24, 23, 22, 21, 20]);
}

#[test]
fn negative_step() {
    let range = 0..5|-2;
    assertEq(range, [4, 2, 0]);
}

#[test]
fn backwards_negative_step() {
    let range = 20..10|-3;
    assertEq(range, [11, 14, 17, 20]);
}
```

### Type Of

Stof's "typeof" expression is handy for determining the type of a value programmatically, especially for "unknown" types and fields. It will always return the base type, one of: <mark style="color:purple;">str</mark>, <mark style="color:purple;">bool</mark>, <mark style="color:purple;">obj</mark>, <mark style="color:purple;">fn</mark>, <mark style="color:purple;">blob</mark>, <mark style="color:purple;">int</mark>, <mark style="color:purple;">float</mark>, <mark style="color:purple;">null</mark>, <mark style="color:purple;">vec</mark>, <mark style="color:purple;">map</mark>, <mark style="color:purple;">set</mark>, or <mark style="color:purple;">Box<</mark>type<mark style="color:purple;">></mark>. Numbers that are a unit type will result in <mark style="color:purple;">float</mark>.

```typescript
fn example() {
    assertEq(typeof "hello", "str");
    assertEq(typeof box(32), "Box<int>");
}
```

### Type Name

Stof's "typename" expression is similar to "typeof", however, will never return a box type ("Box\<type>") and will always give the more complex type if present. For objects, this would be the custom type given to it, or "obj". For numbers that have units, the unit will be returned.

```rust
type CustomType {}
CustomType object: {}

#[test]
fn example() {
    assertEq(typeof self.object, "obj");
    assertEq(typename self.object, "CustomType");

    assertEq(typename "hello", "str");
    assertEq(typename box(32), "int");
    assertEq(typeof box(32), "Box<int>");
    
    assertEq(typename box(33km), "km");
}
```

### Arrow Function

{% hint style="info" %}
See [functions.md](../common-concepts/functions.md "mention") for more information on functions in Stof.
{% endhint %}

```
(<parameters>?): <return_type>? => <expr>
(<parameters>?): <return_type>? => { <statements> }
```

```rust
#[test]
fn example() {
    let f0 = () => {}; // does nothing...
    
    let f1 = (): str => "hello";
    assertEq(f1.call(), "hello");
    
    let f2 = (func: fn): str => func.call();
    assertEq(f2.call(f1), "hello");

    let f3 = (a: int, b: int): int => {
        return a + b;
    };
    assertEq(f3.call(23, 22), 45);
}
```

### Format String

```typescript
#[test("Hello, Nicole Miller")]
fn format(): str {
    let name = "Nicole";
    return `Hello, ${name + ' Miller'}`;
}
```

### Object Constructor

This is how one programmatically creates new objects in the document. Objects created will have the function's scope as its parent.

This does not create a field in the document pointing to the new object. If you don't want to lose your object, create a field for it with an assignment statement (shown below)!

Unless a field is explicitly created for newly created objects, they will not be exported via the standard [formats](formats.md).

{% hint style="warning" %}
Newly created objects are not dropped or reference counted automatically, so keep this in mind, especially for loops, and clean up your document responsibly. For most practical situations, this is not an issue.

Data on the other hand keeps track of the objects it is attached to, so dropping an object will remove data from the document only if that data isn't also referenced elsewhere.
{% endhint %}

```rust
type CustomType {
    field: str;
}

#[test]
fn test() {
    let object = new { // creates a new anonymous object in this document
        field: "hello" // initializes fields on this new object
    };
    
    let custom = new CustomType {
        field: "This is now a CustomType object"
    };
    
    let custom1: CustomType = new {
        field: "Casting this new object via type specifier on declaration"
    };
    
    let custom2 = new {
        field: "Casting this new object via 'as' keyword"
    } as CustomType;
    
    self.myobj = custom2; // creates a field on this object with our new object value
}
```

```rust
#[test]
fn test() {
    // Example of how to clean up after temporary objects that you
    // do not intend to keep around (reference from a field, etc.)
    for (_ in 100) {
        // Create a new object
        let object = new {
            mydata: "some field"
        };
        assert(self.children().len() > 0);
        
        // Do something cool with it...
        
        // When done, drop it
        // Removes sub-objects and data (fields, funcs, etc.) that aren't referenced
        // by other objects in the document.
        drop object;
    }
    assertEq(self.children().len(), 0);
}
```

### Logical And & Or

Logical operations get executed from left to right, with parenthesis taking precedence.

```rust
#[test]
fn test() {
    let first = true;
    let second = false;
    
    // AND
    if (first && second) {
        // do something when both expressions evaluate to a truthy value
        // if "first" evaluates to falsy, "second" is not evaluated/checked
    }
    
    // OR
    if (first || second) {
        // do something if either expression evaluates to a truthy value
        // if "first" evaluates to truthy, "second" is not evaluated/checked
    }
}
```

### Ternary Expression

The ternary expression in Stof is slightly modified from other programming languages, starting with an "if" keyword. If the conditional expression is evaluated to have a truthy value, the first expression after the "?" is executed and returned, otherwise, the expression after the colon ":" is returned.

```rust
#[test]
fn example() {
    let x = if true ? 10 : 20;
    assertEq(x, 10);
    
    x = if false ? 10 : 20;
    assertEq(x, 20);
}
```

### Comparison Operations

```rust
#[test]
fn greater() {
    assert(56 > 10);
    assertNot(10 > 10);
}

#[test]
fn greaterOrEqual() {
    assert(10 >= 10);
    assert(20 >= 10);
    assertNot(10 >= 20);
}

#[test]
fn less() {
    assert(10 < 20);
    assertNot(20 < 10);
}

#[test]
fn lessOrEqual() {
    assert(10 <= 10);
    assert(10 <= 20);
    assertNot(20 <= 10);
}

#[test]
fn equals() {
    assert(10 == 10);
    assertNot(10 == 11);
}

#[test]
fn notEquals() {
    assert(10 != 20);
    assertNot(10 != 10);
}

#[test]
fn notOperator() {
    let val = false;
    assert(!val);
}
```
