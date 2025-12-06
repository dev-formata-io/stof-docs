---
description: First Steps
---

# Quick Start (5 min)

## Hello, World!

{% hint style="success" %}
Also check out the HelloWorld.stof example in the [online playground](https://play.stof.dev/).
{% endhint %}

```rust
#[main]
fn main() {
    pln("Hello, world!");
}
```

Name this file "hello.stof" and run it with the CLI: `stof run hello.stof`. The run command will execute all functions with a `#[main]` attribute, each as a separate async process (no async keyword or attribute needed).

{% hint style="info" %}
This function uses the `Std.pln` library function to print to the standard output stream. The `Std` library is special in that the `Std` portion is optional. Try replacing the statement with **`Std.`**`pln("Hello, world!")`.
{% endhint %}

#### Alternatively, embed Stof within a host (Ex. TypeScript)

```typescript
import { StofDoc } from "jsr:@formata/stof";

const doc = await StofDoc.new();
doc.lib('Std', 'pln', (...args: unknown[])=>console.log(...args));
doc.parse(`
    #[main]
    fn main() {
        pln('Hello, world!');
    }
`);
await doc.run(); // Hello, world!
```

{% hint style="info" %}
For simplicity & minimizing code block size, we'll continue using the CLI for these examples.
{% endhint %}

Stof is a document of data, so we can define our message as a field too:

```rust
message: "Hello, world!"

#[main]
fn main() {
    pln(self.message);
}
```

In this context, "self" is the main root (called "root") object for the document. It helps to think of JSON for comparison, adding functions to it rather than the other way around (valid Stof as-is):

```rust
{
    "message": "Hello, world!"
    
    #[main]
    fn main() {
        pln(self.message); // "self" always references the current object
    }
}
```

The keywords "self" and "super" help navigate which object you'd like to work with, referencing the current object or the parent object, respectively. However, every object can be accessed absolutely by name also.

```rust
str message: "Hello, world!"; // types maintain consistency and readability

#[main]
fn main() {
    pln(root.message); // the name of this object is "root" - the default doc root
}
```

## Fields & Types

Every value within Stof has a type. However, a field can have an explicit type, which will be maintained when values are assigned to it.

```rust
"JSON-syntax": "always accepted", // optional comma or semicolon at the end

str "typed": "a typed field";     // will maintain a str type when assigned to

str cleaner: "easier on the eyes" // optional single/double quotes for field names

const int answer: 42; // will throw an error if assigned to

const str initialized: { // field values are actually expressions (block expr here)
    let a = 30;
    let b = 12;
    return a + b; // evaluated immediately while being parsed
};
```

{% hint style="info" %}
See [types](../core-concepts/types/ "mention") for more information on types in Stof.
{% endhint %}

## Paths

Stof is a graph, so fields and functions are always referenced via a dot-separated path to their location by name, starting at a graph root (or "self"/"super" keywords).

Objects in Stof are also graph nodes, so the terms "node" and "object" are interchangeable in this context.

{% hint style="info" %}
Stof can have as many root nodes as you'd like. The default/main root is named "root", which is the beginning of any field/fn path contained within it or its child nodes.

When traversing the graph via path, paths work both downwards and upwards, looking down before up. For root nodes, upward means sideways.
{% endhint %}

```rust
obj shop: {
    str kind: "Coffee"
    bool roaster: true
    obj contact: {
        str phone: "123-1234"
        str address: "4242 Worldly St."
        
        fn info() -> str {
            `Phone: ${self.phone}\nAddress${self.address}`
        }
    }
}

#[main]
fn main() {
    const is_roaster = root.shop.roaster;
    assert(is_roaster);
    pln(self.shop.contact.info());
}
```
