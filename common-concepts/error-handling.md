---
description: An overview of error handling and debugging in Stof.
---

# Error Handling

Errors in Stof can occur for various reasons. One example is attempting to call a function that doesn't exist. Another might be giving unexpected arguments that cannot be coerced into the right types or forgetting to provide required arguments.

It is also common to throw custom errors from within Stof according to how you expect your interfaces to be used.

Errors can be caught with the try-catch statement to be handled properly or continued up the stack. The catch block can optionally take an error parameter, with one of three available error types:

* <mark style="color:purple;">`str`</mark> - error message only.
* <mark style="color:purple;">`(str, str)`</mark> - error type, then error message respectfully.
* <mark style="color:purple;">`map`</mark> - an entry for "type: str", "message: str", and "stack: vec", providing a function stack to where the error occurred (the actual function that threw being the last in the vector).

{% hint style="info" %}
The "throw" function is located in the [Standard Library](../reference/libraries/standard-library.md) and the Try-Catch statement can be referenced in the [Stof Language Reference](../reference/stof-language.md) page.
{% endhint %}

## Library Error

```rust
#[main]
fn main() {
    try { // using a "try-catch" block here prevents this function from erroring
        let x = Number.ab(-4); // Should be Number.abs(-4)
    } catch (error: (str, str)) {  // only error message if type is "str" instead
        pln(error);
    }
}
```

```
> stof run example.stof
(["NumberLibError(\"NotFound\")", "ab is not a function in the Number Library"])
```

## Throw Custom Error

```rust
#[main]
fn main() {
    try {
        throw("CustomError", "you messed up something");
    } catch (error: (str, str)) {
        pln(error);
    }
}
```

```
> stof run example.stof
(["CustomError", "you messed up something"])
```

## Debugging

{% hint style="info" %}
The "trace" & "callstack" functions can be found in the [Standard Library](../reference/libraries/standard-library.md).&#x20;
{% endhint %}

When an error is thrown in Stof and not handled, it will be printed to the console with its stack information (if using the CLI).

However, it is often nice to programmatically trace the call stack when debugging.

```rust
#[main]
fn main() {
    self.first();
}

fn first() {
    trace("getting to first");
    
    let stack = callstack();
    assertEq(stack.first().name(), "main");
    assertEq(stack.last().name(), "first");
}
```

```
> stof run example.stof
trace main @ root ...
trace first @ root ...
getting to first
```
