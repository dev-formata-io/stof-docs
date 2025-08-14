---
description: Graph roots.
---

# Root Objects

{% hint style="info" %}
Take a look at [introduction-and-design.md](introduction-and-design.md "mention"), it will help you understand this section.
{% endhint %}

A Stof document is a graph that can have multiple root nodes. This feature is very important when combining other documents and APIs, because Stof can essentially act like a file system, storing each document or API under a separate root and treating that root like an independent graph.

{% hint style="info" %}
From lots of experience with Stof, I recommend limiting the number of roots you have and treating roots like separate documents in a file explorer. This will make import/export and large documents feel much simpler and more maintainable, especially with many APIs.
{% endhint %}

## Root Syntax

Within a document, you can always define another root like so. It is much more common, however, to add additional roots via import.

{% hint style="info" %}
Notice how this differs from the field syntax (no colon).
{% endhint %}

```rust
root SecondRoot {
    #[main]
    fn main() {
        assert_eq(self.name(), "SecondRoot");
        assert_not(self.parent());
        pln("Hello, second root");
    }
}

#[main]
fn main() {
    assert_eq(self.name(), "root");
    pln("Hello, main root");
    
    // Can still get to other roots by name!
    assert_eq(SecondRoot.name(), "SecondRoot");
}
```

### Programmatic Root Syntax

You can also create a new root programmatically with a variant of the "new" expression. In this example, I am checking for a root called "ApiRoot" and initializing it if it doesn't exist.

```rust
#[main]
fn main() {
    if (!ApiRoot) ApiRoot = new root {
        root_obj: true
    };
    assert(ApiRoot.is_root());
}
```
