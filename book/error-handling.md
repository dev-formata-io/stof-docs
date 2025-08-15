---
description: Stof throw & try-catch
---

# Error Handling

Errors in Stof use a common try-catch paradigm. Whenever a function cannot be found at runtime, an operator isn't supported, or any other issue occurs, Stof will throw an error that you can optionally catch.

```rust
#[main]
fn main() {
    try throw();
    catch {
        /* recover from an error */
    }
}
```

## Throw

When using `Std.throw(v: unknown)` to throw an error, any Stof value can be passed into the function - this is the value that can be caught.

```rust
#[main]
fn main() {
    try {
        throw("CustomError");
    } catch (error: str) { // will try casting the error to a str if needed
        switch (error) {
            case "CustomError": {
                pln("handling the custom error");
            }
            default: {
                pln("optional handling of an unknown error");
            }
        }
    }
}
```

```rust
#[main]
fn main() {
    try throw(100);
    catch (error: int | str) pln(error); // can use "unknown" or union types too
}
```

As you can imagine, this gets pretty powerful in practice, allowing you to pass objects, maps, functions, etc. back up the stack as an error.

```rust
#[main]
fn main() {
    try throw(() => pln("from a custom handler function"));
    catch (handler: fn) handler();
}
```
