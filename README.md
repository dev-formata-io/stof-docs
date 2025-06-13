---
description: Introduction to Stof.
---

# 🚀 Welcome

[Stof](https://stof.dev) is the future of data representation and utilization. It [unifies and organizes data in layers](book/introduction-and-design.md), providing a simple, efficient, and portable interface into a complex and dynamic environment.

One of the data layers a Stof document can contain is a functional one (code as data & data as code), enabling Stof to manipulate itself as it moves and changes. Shifting logic from the application layer into the data layer improves scalability and security while standardizing data handling. It also provides new methods to tackle pressing challenges like data governance, security, and interoperability.

Designed to work seamlessly with other data formats and standards, Stof adds value to every toolchain, either as a drop-in replacement or as a compliment to existing tools and formats.

Some of the many challenges we are addressing include:

* Moving lightweight logic (as data) over the wire instead of large, sensitive data payloads.
* Unification and translation of data between systems, aiding orchestration and federation.
* Governance, security, and access control.
* Structuring, typing, and schematizing.
* Portable, dynamic, and reusable interfaces (functions, transformations, and business logic).
* Multiple, layered interfaces (code, fields, metadata, meta code, etc. combined).
* Bridging gaps between toolchains and environments.

By addressing these challenges, Stof can help lower development costs, drastically improve developer experience, and lay a better foundation for modern software.

{% hint style="info" %}
Stof is a new open-source project in active development. There's a lot to do! If you have ideas, use cases, or are passionate about these areas of exploration, please [contact us](https://stof.dev/contact-us) and [get involved](resources-and-information.md). The best way you can support us is by [sponsoring this project](https://github.com/dev-formata-io/stof).
{% endhint %}

### Hello, World!

In its most basic form, Stof is just a more friendly JSON document. To begin understanding Stof, it might help to think of it as a JSON document that is turned into a program.

{% hint style="success" %}
**For declaring data, Stof is a superset of JSON - valid JSON will always be valid Stof.**
{% endhint %}

```rust
{
    "message": "Hello, World!"
    
    fn hello() {
        pln(self.message);
    }
}
```

This example is meant to show the connection to JSON, and the simplicity of Stof.

However, we can rewrite this to an equivalent version that looks much nicer. The #\[main] attribute tells Stof which functions to call when we run this document.

```rust
message: "Hello, World!"

#[main]
fn hello() {
    pln(self.message);
}
```

{% hint style="info" %}
See [run.md](reference/cli/run.md "mention") for more information on the Stof CLI. We'll use the CLI for examples instead of embedding Stof in host languages to keep examples centered on Stof.
{% endhint %}

```
> stof run hello.stof
Hello, World!
```

### Example

```rust
users: [
    {
        name: "Joe Schmo",       // commas or semi-colons accepted, but optional
        cm height: 6ft + 1in     // Stof adds units and declarations as expressions
        age: +32;                // trailing commas or semi-colons are okay
    },                           // trailing commas in arrays are okay
]

fn getJoe(): obj {               // Stof adds data types (casting, etc..)
    for (user in self.users) {
        if (user.name.toLower().contains("joe")) return user;
    }
    return null;
}

#[main]
fn main() {
    let joe = self.getJoe();
    pln(stringify(joe, 'toml')); // any format loaded into the doc (parse too)
}
```

```
> stof run example.stof
age = 32
height = 185.42
name = "Joe Schmo"
```

{% hint style="info" %}
For "height", the value is 185.42 because we told Stof the type of that field is "cm", which casts "6ft + 1in" to centimeters. Types are not required for field declarations, but can always be provided. See [units.md](common-concepts/units.md "mention") for more information on units in Stof.
{% endhint %}
