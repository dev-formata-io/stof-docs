---
description: Stof CLI Reference
icon: square-terminal
---

# CLI

The [Stof CLI](https://crates.io/crates/stof-cli) is minimal currently, with only two commands: run and test. Stof's primary use case is to be embedded within a host language, however, the CLI helps us test and document Stof.

Install the CLI with Cargo:

```sh
cargo install stof-cli
```

### Hello, World!

```rust
#[main]
fn main() {
    pln("Hello, world!");
}
```

```
> stof run hello.stof
Hello, world!
```
