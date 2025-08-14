---
description: Libraries linked with Stof types.
---

# Type Libraries

Most types have a library linked with it. For example, the "list" type has a "List" library that has functions for manipulating and working with lists.

```rust
#[main]
fn main() {
    const list = [1, 2, 3];
    assert_eq(List.back(list), 3); // with the library name
    assert_eq(list.back(), 3);     // list automatically references the "List" library
}
```
