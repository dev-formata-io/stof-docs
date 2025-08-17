---
description: Binary Stof format (application/bstf).
---

# Bstf

The "bstf" format allows an entire Stof document to be serialized into a binary blob (or just a specific node/object). It will capture all functions, complex embedded data, attributes, types, fields, etc., and can then be stored or shared (or sent to another environment/runtime to keep running).

```rust
#[main]
fn main() {
    const object = new {
        msg: "hello, world",
        val: 42,
    };
    const bytes = blobify("bstf", object);
    assert_eq(typeof bytes, "blob");

    const dest = new {};
    parse(bytes, dest, "bstf");
    assert_eq(dest.msg, "hello, world");
    assert_eq(dest.val, 42);
}
```
