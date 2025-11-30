---
description: Field & function attributes.
---

# Attributes

Rust-style attributes `#[name(value)]` are used throughout Stof for metadata, behavioral control, and as markers for what/when to execute certain functions.

They can be placed on any field or function, including objects, and are always available programmatically as a map of names to values (str -> unknown).

Attribute values can be any Stof value, including objects, maps, functions, etc. This makes attributes extremely useful for defining validations, schemas, metadata, etc.

{% hint style="success" %}
Attributes have significantly simplified Stof's syntax, and it's a primary design goal to have as little syntax as possible.

Therefore, you'll see attributes used in a lot of places. This also creates a very flexible data format. For example, if you write your own Stof runtime, you can choose to observe the `#[async]` attribute or not.
{% endhint %}

## You've already seen some Stof attributes:

* `#[main]` - marks a function to be run (by default) with the CLI "run" command.
* `#[test]` - marks a function to be run with the CLI "test" command.
* `#[async]` - marks a function to be async (optionally added with the "async" keyword instead).
* `#[type]` - marks an object field to be interpreted by the parser as a formal prototype with a typename (defaults to the field's name).
* `#[private]` - marks a field as only usable programmatically within the object that contains it.

## Create your own or re-purpose attributes as you'd like

```rust
#[meta({"meaning": "everything"})]
field: 42

#[main]
#[purpose("testing general attributes")]
fn main() {
    const my_attributes = this.attributes(); // "this" is the current fn (shorthand)
    pln(my_attributes.get("purpose"));
    
    const field_attributes = self.attributes("field"); // "self" is current doc obj
    const meta = field_attributes.get("meta");
    pln(meta.get("meaning"));
}
```

```bash
> stof run attr.stof
testing general attributes
everything
```

{% hint style="info" %}
Challenge: replace the "meta" map attribute value with an object [via the "new" syntax ](types/#objects)(finally, metadata with types, validation, etc.).

Next, use the same metadata for many fields at once (you can see how this gets [useful and fun](schemas.md)).
{% endhint %}
