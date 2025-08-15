---
description: Stof tests.
---

# Testing

Stof has a thin testing layer built in, just like "run". Test functions are given the attribute #\[test], and are always each ran as a separate asynchronous process.

```rust
#[test]
fn passes() {
    assert(true);
    assert_eq("hi", "hi");
    assert_not(false);
    assert_neq("hi", 42);
}
```

{% hint style="info" %}
Assert functions are a part of the [standard-library-std.md](../libraries/standard-library-std.md "mention").
{% endhint %}

If you expect the test to throw an error, add an #\[errors] attribute as well.

```rust
#[test]
#[errors]
fn passes() {
    throw("this is an expected error");
}
```

## Running Tests

With the CLI, use `stof test` instead of `stof run`. You'll see some nice looking output for each test ran, and then any failures that occurred at the bottom of the output. Errors will include a small stack trace so you can get a good idea of where things went wrong.

{% hint style="info" %}
Errors and debug information is a big area of improvement - please consider helping out if you love Stof.
{% endhint %}
