---
description: YAML format (application/yaml).
---

# YAML

Just like JSON, YAML works with field data. The YAML format has an identifier of "yaml" and is available by default.

{% tabs %}
{% tab title="test.yaml" %}
```yaml
message: 'hello, world'
person:
  name: 'Bob Smith'
  age: 42
```
{% endtab %}

{% tab title="test.stof" %}
```rust
import "./test.yaml";

#[main]
fn main() {
    assert_eq(self.message, "hello, world");
    assert_eq(self.person.name, "Bob Smith");
    assert_eq(self.person.age, 42);
    
    pln(stringify("yaml", self));
}
```
{% endtab %}
{% endtabs %}
