---
description: TOML format (text/toml).
---

# TOML

Just like JSON, TOML works with field data. The TOML format has an identifier of "toml" (the file extension of a TOML file) and is available by default.

{% tabs %}
{% tab title="test.toml" %}
```toml
message = "hello, world"

[person]
name = "Bob Smith"
age = 42
```
{% endtab %}

{% tab title="test.stof" %}
```rust
import "./test.toml" as self.Toml;
import text "./test.toml" as self.Cmp; // uses "text" format instead of "toml"

#[main]
fn main() {
    assert_eq(self.Toml.message, "hello, world");
    assert_eq(self.Toml.person.name, "Bob Smith");
    assert_eq(self.Toml.person.age, 42);
    
    const toml = stringify("toml", self.Toml); // string export (fields only)
    assert_eq(toml, self.Cmp.text); // from file string import above
}
```
{% endtab %}
{% endtabs %}
