---
description: Bytes format (application/octet-stream).
---

# Bytes

Just like the text format, the "bytes" format imports/exports binary (blob) data to/from a "bytes" field.

{% hint style="info" %}
This format is handy as it allows virtually all imports (into an abstract blob). Just use it as an explicit format with the following syntax: `import bytes "mything.bin"`.
{% endhint %}

```rust
#[main]
fn main() {
    const bytes: blob = "hello, world";
    const object = new {};
    parse(bytes, object, format = "bytes");
    
    assert_eq(typeof object.bytes, "blob");
    assert_eq(object.bytes as str, "hello, world");
    
    const export = blobify("bytes", object);
    assert_eq(export as str, "hello, world");
}
```
