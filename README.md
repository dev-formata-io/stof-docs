---
description: Introduction.
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# 🚀 Stof: The Executable Data Format

{% hint style="success" %}
Executable Data for Distributed Systems, AI/ML workflows, Smart Configs, & Pipelines.
{% endhint %}

{% hint style="info" %}
Stof stands for "Standard Transformation and Organization Format", and also means "fabric" in Dutch.
{% endhint %}

A unified data-logic format that works seamlessly with other formats to bridge the gap between static data and programmable documents.

Based on an "Everything as Data" approach, in which fields, functions, PDFs, images, binaries, or any other type of data are neatly combined, while keeping single-document simplicity and portability.

This provides many benefits:

* Write data once, use it everywhere, in any format
* Sandboxed logic + execution in your data (as data)
* Send functions over APIs
* Doesn't need a large ecosystem to work
* Format-agnostic (works with JSON, YAML, TOML, PDF, binaries, etc.)

## Example Use-Cases

* Smart configs with validation and logic
* Data interchange with sandboxed execution
* Prompts as human-readable & maintainable data + code
* AI/LLM workflows/pipelines & model configs
* Data pipelines with built-in processing & validation
* Integration glue between systems
* Self-describing datasets
* ... basically anywhere data meets logic

## Sample

{% hint style="success" %}
Check out the [online playground](https://play.stof.dev/) for real examples you can play with right now.
{% endhint %}

```rust
#[attributes("optional exec control | metadata | meta-logic")]
// A field on the doc "root" node.
field: 42

// JSON-like data & function organization
stats: {
    // Optional field types & expressions
    prompt context: prompt("trees of strings", tag="optional-xml-tag",
        prompt("behaves like a tree for workflows & functions"),
        prompt("just cast to/from str anywhere strings are needed")
        // Std.prompt(..) can take N prompts as sub-prompts
    );
    
    // Units as types with conversions & casting
    cm height: 6ft + 2in
    MiB ram: 14GiB + 50GiB - 5GB
}

#[main]
/// The CLI (and other envs) use the #[main] attribute for which fns to call on run.
fn do_something() {
    // Dot separated path navigation of the document (self is the current node/obj)
    let gone = self.self_destruction();
    assert(gone);

    // async functions, blocks, and expressions always available
    async {
        const now = Time.now();
        loop {
            sleep(20ms);
            if (Time.diff(now) > 2s) break;
        }
    }

    // partial I/O with any format
    pln(stringify("toml", self.stats));
}

/**
 * A function that removes itself from this document when executed.
 */
fn self_destruction() -> bool {
    pln(self.field); // Std.pln(..) print line function
    drop(this);      // "this" is always the last fn on the call stack
    true             // "return" keyword is optional (no ";")
}
```

## Embedded

Stof is written in Rust and can be embedded today in TypeScript/JavaScript (via WebAssembly), or within your Rust project.

{% hint style="success" %}
Several languages are planned, including Python, Go, and a hosted Runtime-as-a-Service. Please reach out via Discord to discuss timelines and prioritization.
{% endhint %}

### Rust

```toml
[dependencies]
stof = "0.8.*"
```

```rust
use stof::model::Graph;

fn main() {
    let mut graph = Graph::default();
    
    graph.parse_stof_src(r#"
        #[main]
        fn main() {
            pln("Hello, world!");
        }
    "#, None).unwrap();

    match graph.run(None, true) {
        Ok(res) => println!("{res}"),
        Err(err) => panic!("{err}"),
    }
}
```

### TypeScript

The package is hosted on [JSR](https://jsr.io/@formata/stof) for you to use in the JS environment of your choice.

```typescript
import { StofDoc } from '@formata/stof';
const doc = await StofDoc.new();

doc.lib('Std', 'pln', (... vars: unknown[]) => console.log(...vars));
doc.lib('Example', 'nested', async (): Promise<Map<string, string>> => {
    const res = new Map();
    res.set('msg', 'hello, there');
    res.set('nested', await (async (): Promise<string> => 'this is a nested async JS fn (like fetch)')());
    return res;
}, true);

doc.parse(`
    field: 42
    fn main() -> int {
        const res = await Example.nested();
        pln(res);
        self.field
    }
`);
const field = await doc.call('main');
console.log(field);

/*
Map(2) {                                                                                                                                                                                                                       
  "msg" => "hello, there",
  "nested" => "this is a nested async JS fn (like fetch)"
}
42
*/
```

## Why Now?

In the current technology landscape, every program is distributed, utilizing multiple systems in parallel to deliver value. In distributed systems, everything is about moving either data to computation or computation to data.

> Stof proposes: Why not move them together as a unified entity?

Stof explores:

* Practical code mobility at scale with modern type systems
* Unifying data transformation with code distribution
* Security models for distributed computation-as-data
* Performance characteristics of serializable computation vs traditional RPC/message-passing
* Formal semantics for "code as data" in distributed systems
* Edge computing, data pipelines, and collaborative systems

### The Serializable Computation Problem

Code mobility involves either strong mobility (moving code, data, and execution state) or weak mobility (moving just code and data). This has been a challenge for decades. Stof's "Everything as Data" approach, including functions and types that can be serialized and sent over APIs, directly addresses this.

### Actor Model + Data-Oriented Programming

What if you could send not just messages, but **executable transformations** as data between actors? This bridges:

* The actor model's isolation and message-passing
* Data-oriented programming's focus on data transformation pipelines
* Code mobility's ability to move compuation to data

### Edge Computing & Data Locality

Rather than fixing the interface to a resource, a minimal interface can be defined and code implementing higher-level interfaces placed alongside it as required, allowing application-specific interaction patterns.

Imagine: An IoT sensor network where you push Stof transformation functions to edge devices. The functions are data, so they can be:

* Versioned and rolled back
* Inspected for resource usage before execution
* Composed and optimized at runtime
* Migrated between nodes based on data locality

### Distributed Data Pipelines

Self-describing data with embedded transformations reduces the complexity of distributed data pipelines compared to current orchestration-heavy approaches.

* Transformations travel with the data
* Self-validating data that includes its own processing logic
* Dynamic pipeline reconfiguration without redeployment

### Secure Multi-Party Computation

Sandboxing restricts mobile code to a controlled environment with limited system resource access. Stof's sandbox + serializable functions enable:

* Controlled computation on sensitive data
* Verifiable transformations (the code is inspectable data)
* Dynamic permission models (capabilities as data)

### Collaborative Editing & CRDTs

Conflict-free Replicated Data Types (CRDTs) struggle with complex business logic. Stof's operations as first-class data enable richer collaborative systems than current CRDT approaches.

* Merged and reordered data
* Validated before application
* Composed with other operations & structures

## Feedback & Community

We welcome contributors and feedback! The community is growing, and this is a new project with a lot of potential.

* Open issues or discussions on [GitHub](https://github.com/dev-formata-io/stof)
* Please join the [Discord](https://discord.gg/Up5kxdeXZt) to get involved and/or discuss Stof
* Email **info@stof.dev** to contact us directly
