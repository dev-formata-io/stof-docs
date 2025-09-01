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

# 🚀 Welcome

## [Stof](https://stof.dev/): Because 47 Data Formats Weren't Enough

{% hint style="success" %}
**Plot twist: This format actually unifies them all (even complex data, like PDF, DocX, Images, etc.).**
{% endhint %}

Stop juggling YAML, JSON, TOML, and custom scripts. Define once and use everywhere, with Stof, the simplest and safest way to unify and transform data in a singular, programmable data interface.

> Combines the simplicity and flexibility of JSON with the power of programming when you need it, always under your control.

{% hint style="info" %}
Works **with** other data formats (both import & export), so you don't have to pick & choose.
{% endhint %}

## :earth\_americas: Real-World Use-Cases

* **Data-Mesh, Integration, & Orchestration** glue-layer between systems
* **Universal LLM & AI** workflows, tools, & conversational data
* **Smart Configs** with logic, units, and validation
* **AI Model Configs** that package ML models with preprocessing logic
* **API Definitions** and transformations with [schemas](book/schemas.md)
* **Self-Describing Datasets** that automatically tag and validate themselves
* **Data pipelines** with built-in processing and optimization
* **Templates** that generate other formats

## :crystal\_ball: The Difference: It's All Just Data

Unlike traditional programming languages, **everything in Stof is data (functions too)**:

{% hint style="success" %}
Drop-in replacement that works with your existing JSON/YAML/TOML (and many other) files.
{% endhint %}

* **Store it** in databases
* **Send it** over the wire with/as APIs (types and functions too)
* **Merge, split, import, & export** data in the format(s) of your choice
* **Transform** data between systems
* **Embed** within the environment of your choice

## :bulb: Why Developers Need Lots of Stof

### "Holy sh\*t, my configs actually work now"

Configurations with logic and validation that won't fail you when it matters.

```rust
database: {
    const str host: env("DB_HOST") ?? "localhost" // logic & types!
    int max_connections: 100
    ms timeout: 3s                                // units!
    GiB storage: 2TiB
    
    // validation
    fn validate() -> bool {
        const connections = self.max_connections;
        connections > 0 && connections < 1_000 && self.timeout > 100ms
    }
    
    // fixes itself
    fn tune(load: int) {
        self.max_connections = min(load * 2, 500);
    }
}

#[main]
// deploy with confidence
fn deploy() {
    assert(self.database.validate());
    
    // ops team will thank you
}
```

### "Wait, I can stop maintaining 17 different files?"

Before Stof, you probably had:

* `docker-compose.yml` (but different for each env)
* `kubernetes/production.yaml` (copy-pasted from dev)
* `monitoring/alerts.json` (never updated)
* so many more...

#### After Stof:

```rust
import "@docker"; // I/O any type of data (Stof, JSON, TOML, PDF, DOCX, PNG, etc.)
import "template.yml" as self.ComposeTemplate;

const app: {
    name: "my-service";  // optional comma or semi-colon (trailing too)
    version: 1.2.3-beta  // semantic versions as a base type
}

fn docker_compose(env: str = "staging") -> DockerCompose {
    /* generate compose using self.ComposeTemplate fields */
}
fn kubernetes_manifest(env: str = "staging") -> obj { /* generate k8s obj */ }
// ... anything else you need

// One source of truth, infinite possibilities
```

### "My users can finally script safely"

With Stof's customizable sandbox, control every aspect of how users can interact:

```rust
#[handler]
// Untrusted user code, sent with the request
fn custom_endpoint_handler() -> Response {
    let c = fs.read("path"); // error - file sys not available inside sandbox
    
    new Response {
        user_id: data.user.id,
        timestamp: Time.now() as seconds,
        processed: true,
    }
}

// You sleep peacefully knowing:
// ❌ They can't access your filesystem  
// ❌ They can't make network calls
// ❌ They can't see other users' data
// ✅ They CAN solve their problems
```

## :eyes: Quick Overview

```rust
const list users: [              // optional field type and const
    {
        name: "Joe Schmo",       // commas or semi-colons accepted, but optional
        cm height: 6ft + 1in     // unit types and declarations are expressions
        age: +32;                // trailing commas or semi-colons
    },                           // trailing commas in arrays
]

fn joe() -> obj {                // functions, fields, and complex data
    for (const user in self.users) {
        if (user.name.lower().contains("joe")) return user;
    }
    null
}

#[main]                          // func & field attributes for control
#[custom({'ex': true})]          // metadata values (funcs, maps, objs, etc.)
fn main() {
    const joe = self.joe();
    assert(this.attributes().get("custom").get("ex"));
    
    async {                                // async at the core (funcs & exprs too)
        let body = stringify("toml", joe); // format I/O (binary & parse too)
        body.push("stof = true\n");
        pln(body);
    }
}
```

```bash
> stof run example.stof
age = 32
height = 185.42
name = "Joe Schmo"
stof = true
```

{% hint style="success" %}
JSON object & field syntax is also supported.
{% endhint %}

### Crates & Versions

{% hint style="info" %}
[Stof](https://crates.io/crates/stof): `v0.8.*`

[CLI](https://crates.io/crates/stof-cli): `v0.11.*`
{% endhint %}
