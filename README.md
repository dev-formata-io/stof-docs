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

# 🚀 Stof: Data that carries its own logic

Send functions + data over APIs, write configs that validate themselves, build data pipelines where transformations travel with the data, store logic + data in a database, etc.

{% hint style="success" %}
Works with JSON, YAML, TOML, etc. - no migration needed.

Add/import logic only where required.
{% endhint %}

Treats everything uniformly - fields, functions, PDFs, images, binaries, etc. - as data that can be combined in a single portable document.

{% hint style="info" %}
Stof is the Standard Transformation and Organization Format.
{% endhint %}

{% embed url="https://play.stof.dev" %}

## :wave: New to Stof?

1. Read the 5-minute [Quick Start](getting-started/quick-start-5-min.md)
2. Try the [online playground](https://play.stof.dev/)
3. Follow the [TypeScript Config Tutorial](getting-started/tutorial-stof-+-typescript-config.md)

## Benefits

* Write data + logic once, use it everywhere (JS, Rust, Python, anywhere your app lives)
* Format-agnostic I/O (works with JSON, YAML, TOML, PDF, binaries, etc.)
* Sandboxed logic + execution in your data (as data)
* Send functions over APIs
* Store data + logic in your database
* Doesn't need a large ecosystem to work

## Use-Cases

* Smart configs with validation and logic
* Data interchange with sandboxed execution
* Prompts as human-readable & maintainable data + code
* AI/LLM workflows and model configs
* Data pipelines with built-in processing
* Integration glue between systems
* Self-describing datasets
* ... basically anywhere data meets logic

## Who is using Stof?

Stof is being used by many organizations already:

| Company                                           | Description                                                                                      | Contributor Status                                                  |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
| ![Formata](<.gitbook/assets/Formata (3) (1).png>) | [Formata](https://www.formata.io/) is the Customer Intelligence Infrastructure for RevOps teams. | :white\_check\_mark: Contributor and a committed maintainer         |
| Your logo here                                    | Reach out on [Discord](https://discord.gg/Up5kxdeXZt)                                            | Check out the [GitHub repo](https://github.com/dev-formata-io/stof) |

## Examples

Here's what Stof looks like in practice. Notice how functions, data, and even unit conversions live together in one document.

Stof documents can be executed locally with the [CLI](getting-started/installation.md), embedded within the language of your choice, or on your browser via the online playground.

{% hint style="success" %}
Check out the [online playground](https://play.stof.dev/) for real examples you can play with right now.
{% endhint %}

### Data Format + Logic

Stof is a data format, similar to JSON, YAML, TOML, etc., with functions.

```rust
// Defines data (fields, funcs, etc.)
server: {
    port: 8080
    address: "localhost"
    memory: 500GiB + 50MB
    ttl: 300s
    
    fn url() -> str {
        "https://" + self.address + ":" + self.port
    }
    fn valid() -> bool {
        self.port > 1024 && self.port <= 65536 && self.address != ""
    }
}
```

### Data Transformation

Manipulate the document using the functional data it contains.

{% hint style="info" %}
Try this example in the [playground](https://play.stof.dev/blank?content=%0Adata%3A%20%5B1%2C%202%2C%203%2C%204%2C%205%5D%0A%0A%23%5Bmain%5D%0Afn%20main%28%29%20%7B%0A%20%20%20%20for%20%28let%20i%20in%20%26self.data%29%20i%20%2A%3D%202%3B%0A%20%20%20%20pln%28self.data%29%3B%0A%7D%0A%0A%2F%2A%20Output%3A%0A%5B2%2C%204%2C%206%2C%208%2C%2010%5D%0A%2A%2F%0A).
{% endhint %}

```rust
data: [1, 2, 3, 4, 5]

#[main]
fn main() {
    for (let i in &self.data) i *= 2;
    pln(self.data);
}

/* Output:
[2, 4, 6, 8, 10]
*/
```

### Workflows & Pipelines

Stof is a lightweight document format where workflows are portable, executable data. See an example [here](getting-started/tutorial-stof-+-typescript-config.md#completed-example).

{% hint style="success" %}
Stof has [prototypes](core-concepts/types/prototypes.md), which make workflows much simpler, more powerful, and more maintainable.
{% endhint %}

{% code expandable="true" %}
```rust
workflow: {
    #[run(0)]
    remote-api: {
        address: "https://my-server.com"
        
        #[run]
        fn load_remote_stof_api() {
            const resp = await Http.fetch(self.address);
            Std.assert(Http.success(resp));
            
            // parse more stof data + logic into this doc as root obj named Api
            const stof = Http.text(resp);
            Std.parse(stof, "Api", format="stof");
            
            assert(Api.version); // Std lib is implied
            assert(Api.version > 1.4.4);
        }
    }
    
    #[run(1)]
    call-api: {
        #[run]
        fn use_loaded_api() {
            // do something with the loaded Stof API
        }
    }
}

#[main]
fn main() {
    // Obj.run(obj) recursively runs all #[run] fields & funcs with opt order
    self.workflow.run();
}
```
{% endcode %}

### Self-Validating Data

Beyond simple functions, Stof supports comprehensive [schemas](common-patterns/schemas.md) for validations and transformations.

{% code expandable="true" %}
```rust
#[type]
Server: {
    #[schema((target_val: int):bool => target_val > 1024 && target_val <= 65536)]
    int port: 8080

    #[schema((target_val: str):bool => target_val != "")]
    // the ! at the end of a type means "never null", will throw if null is assigned
    str! address: "localhost"

    #[schema((target_val: MiB):bool => target_val > 2MB)]
    MiB memory: 500GiB

    #[schema((target_val: ms):bool => target_val > 1s)]
    ms ttl: 300s

    fn url() -> str {
        `${self.address.starts_with("http") ? "" : "https://"}${self.address}:${self.port}`
    }

    fn validate() {
        // <Server> is a path resolution to the prototype obj
        // self is the actual instance obj (in this case has a prototype of Server)
        assert(<Server>.schemafy(self));
    }
}

// import into the doc from TOML, JSON, YAML, etc.
old_json: r#"
{
    "port": 4000,
    "address": "https://my-server.com",
    "memory": 3000,
    "ttl": 5000
}
"#

#[main]
fn main() {
    const my_server = new {};
    parse(self.old_json, my_server, "json");
    my_server as Server;   // cast to the Server prototype

    my_server.validate();
    pln(my_server.url());
    
    pln(stringify("toml", my_server)); // export TOML
}

/* Output:
https://my-server.com:4000
address = "https://my-server.com"
memory = 3000.0
port = 4000
ttl = 5000.0
*/
```
{% endcode %}

### Prompt Management

Primitive types designed for modern needs & workflows (prompts, unit types, const fields, etc.).

{% code expandable="true" %}
```rust
fn background() -> prompt {
    const background = prompt(tag="background");
    background.push("\nSeamless str <-> prompt casting & flows.");
    background.push("\nMakes working with AI more maintainable for humans.\n");
    background
}

fn format() -> prompt {
    // Std.prompt(..) can take N sub-prompts as a tree after text & tag
    prompt("", tag="formatting",
        "\n",
        prompt("Introduction", tag="first"),
        "\n",
        prompt("Presentation", tag="second"),
        "\n"
    )
}

fn my_prompt() -> prompt {
    const res = prompt();
    res.push(self.background());
    res.push("\n");
    res.push(self.format());
    res.push("\n");
    res.push(prompt("Do something cool, AI", "instructions"));
    res
}

#[main]
fn main() {
    // prompt is a tree of strings when you're working with it
    // and casts to a string when you need it to be
    const str_prompt: str = self.my_prompt();
    pln(str_prompt);
}

/* Output:
<background>
Seamless str <-> prompt casting & flows.
Makes working with AI more maintainable for humans.
</background>
<formatting>
<first>Introduction</first>
<second>Presentation</second>
</formatting>
<instructions>Do something cool, AI</instructions>
*/
```
{% endcode %}

### Foundations for Tooling

Stof provides a foundation for portable challenge/response logic, programmatic sharing and invocation of APIs, and more.

{% code expandable="true" %}
```rust
Data<Age> passport: Age.generate() 
blob encrypted_json: null

fn init_encrypted() {
    let payload = new { version: 0.1.5-agent.beta, msg: "Hello, world!" };
    let res = Age.blobify(self.passport, "json", payload);
    self.encrypted_json = res;
    drop(payload);
    pln("Init Encrypted:\n", stringify("toml", self));
}

#[main]
fn main() {
    self.init_encrypted();

    let decrypted = new {};
    Age.parse(self.passport, self.encrypted_json, decrypted, "json");
    pln("\nDecrypted TOML:\n", stringify("toml", decrypted));
}

/* Output:
Init Encrypted:
encrypted_json = [97, 103, 101, 45, 101, 110, 99, 114, 121, 112, 116, 105, 111, 110, 46, 111, 114, 103, 47, 118, 49, 10, 45, 62, 32, 88, 50, 53, 53, 49, 57, 32, 98, 82, 86, 70, 55, 103, 88, 112, 84, 53, 106, 80, 112, 108, 71, 77, 100, 83, 67, 65, 122, 121, 113, 49, 49, 85, 107, 66, 72, 70, 48, 117, 100, 111, 115, 73, 114, 53, 88, 76, 82, 106, 56, 10, 88, 72, 50, 108, 81, 117, 74, 83, 82, 81, 117, 71, 88, 110, 90, 112, 99, 98, 49, 119, 57, 102, 55, 66, 111, 76, 100, 51, 66, 73, 54, 54, 77, 81, 102, 107, 116, 66, 50, 43, 67, 89, 48, 10, 45, 62, 32, 96, 62, 103, 91, 60, 35, 40, 45, 103, 114, 101, 97, 115, 101, 32, 84, 79, 35, 43, 95, 41, 122, 54, 32, 61, 84, 96, 71, 105, 94, 103, 10, 80, 79, 54, 86, 67, 47, 112, 56, 113, 113, 108, 86, 101, 122, 66, 56, 112, 78, 67, 119, 105, 49, 67, 50, 87, 113, 87, 106, 83, 106, 65, 101, 110, 117, 68, 73, 65, 86, 76, 55, 65, 104, 102, 102, 71, 107, 67, 108, 54, 101, 108, 56, 119, 84, 55, 81, 72, 113, 103, 10, 45, 45, 45, 32, 98, 75, 76, 106, 109, 115, 108, 54, 101, 57, 78, 80, 97, 55, 49, 114, 49, 76, 76, 78, 116, 116, 88, 68, 112, 48, 90, 106, 109, 57, 48, 81, 53, 89, 48, 113, 114, 75, 65, 48, 81, 47, 107, 10, 229, 30, 32, 67, 211, 112, 104, 120, 146, 182, 247, 254, 133, 248, 67, 77, 75, 133, 218, 19, 138, 2, 70, 197, 74, 184, 109, 156, 89, 51, 116, 254, 221, 80, 223, 31, 246, 199, 1, 200, 202, 130, 45, 165, 55, 50, 74, 191, 123, 103, 241, 139, 244, 17, 163, 14, 31, 27, 162, 217, 151, 196, 101, 85, 251, 69, 7, 5, 205, 154, 118, 196, 113, 51, 38, 75, 5, 155, 186, 150, 132, 57, 218, 213]

Decrypted TOML:
msg = "Hello, world!"
version = "0.1.5-agent.beta"
*/
```
{% endcode %}

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
