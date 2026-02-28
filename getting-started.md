---
description: Pick your environment and get something running.
---

# Getting Started

All examples use the same Stof document.

## The Example

Save this as `hello.stof`:

```rust
str message: "Hello, Stof!"

fn greet() -> str {
    `${self.message} Data and logic, together.`
}

#[main]
fn main() {
    pln(self.greet());
}

/* Output:
Hello, Stof! Data and logic, together.
*/
```

***

## CLI

**Install:**

```bash
cargo install stof-cli
```

{% hint style="info" %}
Don't have Rust? [Install it here](https://doc.rust-lang.org/book/ch01-01-installation.html) - cargo comes with it.
{% endhint %}

**Run:**

```bash
stof run hello.stof
```

{% hint style="info" %}
VS Code user? Search "Stof" in the extensions tab for syntax highlighting.
{% endhint %}

***

## TypeScript

**Install:**

```bash
npm i @formata/stof
```

**Run:**

```typescript
import { initStof, StofDoc } from '@formata/stof';
await initStof();

const doc = new StofDoc();
doc.lib('Std', 'pln', (...args: unknown[]) => console.log(...args));
doc.parse(`
    str message: "Hello, Stof!"

    fn greet() -> str {
        \`\${self.message} Data and logic, together.\`
    }

    #[main]
    fn main() {
        pln(self.greet());
    }
`);
await doc.run(); // Hello, Stof! Data and logic, together.
```

***

## Python

**Install:**

```bash
pip install stof
```

**Run:**

```python
from pystof import Doc

doc = Doc()
doc.parse("""
    str message: "Hello, Stof!"

    fn greet() -> str {
        `${self.message} Data and logic, together.`
    }

    #[main]
    fn main() {
        pln(self.greet());
    }
""")
doc.run()
# Hello, Stof! Data and logic, together.
```

***

## Rust

Add to `Cargo.toml`:

```toml
[dependencies]
stof = "0.9.*"
```

**Run:**

```rust
use stof::model::Graph;

fn main() {
    let mut graph = Graph::default();

    graph.parse_stof_src(r#"
        str message: "Hello, Stof!"

        fn greet() -> str {
            `${self.message} Data and logic, together.`
        }

        #[main]
        fn main() {
            pln(self.greet());
        }
    "#, None).unwrap();

    graph.run(None, true).unwrap();
    // Hello, Stof! Data and logic, together.
}
```

***

## What's next?

* [Try the playground](https://play.stof.dev/) — no install required
* [Core Concepts](core-concepts/design.md) — understand how Stof works
* [Schemas](common-patterns/schemas.md) — self-validating data
* [Async](advanced-features/async.md) — parallel execution
