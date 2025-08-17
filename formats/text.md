---
description: Text format (text/plain).
---

# Text

The "text" format is used to import and export plain text from a "text" field.

{% tabs %}
{% tab title="test.txt" %}
```
hello, there
```
{% endtab %}

{% tab title="test.stof" %}
```rust
import "./test.txt"; // "text" format also has "txt" as an identifier
// import text "./test.txt"; // also works

#[main]
fn main() {
    assert_eq(self.text, "hello, there");
    assert_eq(stringify("text", self), "hello, there");
}
```
{% endtab %}
{% endtabs %}
