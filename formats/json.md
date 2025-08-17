---
description: Stof & JSON
---

# JSON

JSON is Stof's closest relative format. The Stof parser accepts JSON field & object syntax (most JSON files can be considered Stof).

Stof, unlike JSON, is not limited to just fields of data, however. Because of this, the JSON format implementation is a lossy export, but captures everything on import.

{% hint style="info" %}
Take a look at [imports.md](../book/imports.md "mention") and [exports.md](../book/exports.md "mention") if you haven't already.
{% endhint %}

{% tabs %}
{% tab title="test.json" %}
```json
{
    "message": "hello, world",
    "person": {
        "name": "Bob Smith",
        "age": 42
    }
}
```
{% endtab %}

{% tab title="test.stof" %}
```rust
import "./test.json"; // uses "json" format

#[main]
fn main() {
    assert_eq(self.message, "hello, world");
    assert_eq(self.person.name, "Bob Smith");
    assert_eq(self.person.age, 42);
    
    const json = stringify("json", self); // string export (fields only)
    assert_eq(json, '{"message":"hello, world","person":{"name":"Bob Smith","age":42}}');
}
```
{% endtab %}
{% endtabs %}
