---
description: Export data from Stof.
---

# Exports

Stof can export any format for which it has an implementation. It is up to each format implementation to decide how the export works - most are lossy (JSON, for example, can only export fields). Some are binary only, some use directories (like "docs"), and some only support a specific field, where content is expected to live.

Formats are extensible and replaceable, though, so feel free to create your own! And share with the Stof community so that everyone can benefit.

## Std.stringify & Std.blobify

The most common way to export data is by using the [standard-library-std.md](../libraries/standard-library-std.md "mention"). In addition to `Std.stringify(..)` (which might already feel familiar), the library also offers `Std.blobify(..)`, which uses the `binary_export` function of your desired (and loaded) format implementation.

{% hint style="info" %}
All formats that implement a string export also support a binary export - Stof converts the strings to UTF-8 when needed.
{% endhint %}

#### Example

```rust
#[main]
fn main() {
    const object = new {
        a: "hello",
        b: 42,
        c: true,
    };
    
    const json: str = stringify("json", object); // string export
    const bytes: blob = blobify("json", object); // binary export
    
    assert_eq(json, '{"a":"hello","b":42,"c":true}');
    assert_eq(bytes as str, json);
}
```
