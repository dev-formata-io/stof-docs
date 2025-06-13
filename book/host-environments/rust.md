---
description: Embedding Stof in Rust.
---

# Rust

Because Stof is written in Rust, there is a lot of support for using Stof with Rust.

{% hint style="info" %}
Take a look at our [GitHub repository](https://github.com/dev-formata-io/stof) to see how Stof is implemented.
{% endhint %}

To add Stof to your Rust project, use cargo:

```bash
cargo add stof
```

The main interfaces to Stof are the document (SDoc) and graph (SGraph). If you're working primarily with the Stof language interface (like in the rest of these documents), you won't need to interact with the graph. However, if you need deeper control, the graph organizes and stores the data.

{% hint style="info" %}
Make sure to check out the [introduction and design](../introduction-and-design.md) of Stof. This will help you get started with deep programmatic control over your Stof data in Rust.
{% endhint %}

## Hello, World!

The classic first program. A document can be constructed from a string, file, or binary (Vec\<u8>) (each with a format specifier). In this case, we are constructing a document from a Stof string.

{% hint style="info" %}
The Stof "language" is just a format that gets parsed, just like any other in Stof. As such, it can be parsed into the document at any time, just like JSON or any other format.
{% endhint %}

```rust
use stof::SDoc;

fn main() {
    let mut doc = SDoc::src(r#"
        #[main]
        fn main() {
            pln('hello, world!');
        }
    "#, "stof").unwrap();

    let _ = doc.run(None);
}
```

```
> cargo run
hello, world!
```
