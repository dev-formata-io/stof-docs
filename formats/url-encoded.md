---
description: URL-encoded format (application/x-www-form-urlencoded).
---

# URL-Encoded

It's very common (especially with older APIs) to use a URL-encoded format as a message body instead of JSON.

{% hint style="info" %}
You've probably picked up on how formats work by now - the file extension ends up as the default format identifier. So, even though a URL-encoded file format doesn't really exist, if you have a file with the extension "urlencoded", you could import it (would auto-reference this format).
{% endhint %}

```rust
#[main]
fn main() {
    const object = new {
        a: "hello",
        b: 42,
        c: true,
    };
    const encoded = stringify("urlencoded", object);
    assert_eq(encoded, "a=hello&b=42&c=true");
    
    const import = new {};
    parse(encoded, import, "urlencoded");
    assert_eq(import.a, "hello");
    assert_eq(import.b, 42);
    assert_eq(import.c, true);
}
```
