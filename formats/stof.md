---
description: Stof format (application/stof).
---

# Stof

Stof is a format, just like all of the others. This makes it available to use with `Std.parse(..)` also!

{% hint style="info" %}
Check out [custom-embedded.md](../book/custom-embedded.md "mention") and [imports.md](../book/imports.md "mention") for more information.
{% endhint %}

{% tabs %}
{% tab title="main.stof" %}
```rust
import "./data"

#[main]
fn main() {
    assert_eq(self.message, "hello, world");
    assert_eq(self.person.name, "Bob Smith");
    assert_eq(self.person.age, 42);
}
```
{% endtab %}

{% tab title="data.stof" %}
```rust
message: "hello, world"
person: {
    name: "Bob Smith"
    age: 42
}
```
{% endtab %}
{% endtabs %}
