---
description: Stof's standard boolean library ("Bool").
icon: toggle-on
---

# Boolean Library

## Common Value Functions

### <mark style="color:purple;">Bool</mark><mark style="color:orange;">.toString</mark>(val: <mark style="color:green;">bool</mark>): <mark style="color:green;">str</mark>

Returns this boolean value converted to a string (either "true" or "false").

### <mark style="color:purple;">Bool</mark><mark style="color:orange;">.or</mark>(val: <mark style="color:green;">bool</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let val = self.dne.or(false);
    assertEq(val, false);
}
```
