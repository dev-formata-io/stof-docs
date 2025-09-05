---
description: Stof format (application/stof).
---

# Stof

Stof language is implemented as a Stof format, just like the others (JSON, YAML, TOML, etc.). This makes it available to use with `Std.parse(..)` also!

{% hint style="info" %}
Check out [custom-embedded.md](../book/custom-embedded.md "mention") and [imports.md](../book/imports.md "mention") for more information.
{% endhint %}

{% hint style="warning" %}
You might be thinking there must be security concerns with this. However, the host environment (running Stof) has complete and sandboxed control over libraries, which is the only way Stof can interact with that machine.

This opens up use cases across systems where you could send an API to be used (functions, schemas, types, etc.) in addition to data.
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
    
    const stof = r#"
        parsed: true

        fn say_hi(name: str) -> str {
            `Hi, ${name}`
        }
    "#;
    const container = new {};
    parse(stof, container, "stof"); // parse just like any other format

    assert(container.parsed);
    assert_eq(container.say_hi("John Snow"), "Hi, John Snow");
    drop(container); // can remove from the doc when you're done
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

{% hint style="info" %}
Challenge: what if `drop(this)` is called within a function? The "this" references the current function as a "fn" pointer, so it would work just fine, removing the currently executing function from the document (surprisingly helpful in some cases).

In Stof, everything is data and can be manipulated as such, including the currently running environment.
{% endhint %}
