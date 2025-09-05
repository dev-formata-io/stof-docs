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

> <mark style="color:$primary;">Stof exists to solve the fragmentation and brittleness of data and configuration across systems.</mark>

Modern software (especially AI/ML, infra, cloud, CI/CD, and workflows) increasingly relies on structured data that needs to be:

* Human-readable
* Machine-validatable
* Extendable with logic
* Executable safely
* Translatable between formats
* Versioned and inspectable

But the tools we have for this are _primitive and fragmented_:

* JSON/YAML/TOML carry structure, but rely on other tools for behavior, units, schemas, or validations.
* External tools create complexity between systems and often require configuration themselves.
* Configs drift and break across environments.
* Runtime logic and validation are scattered across codebases, devops scripts, and data definitions.

> <mark style="color:$primary;">Stof unifies</mark> <mark style="color:$primary;"></mark><mark style="color:$primary;">**structure + validation + behavior**</mark> <mark style="color:$primary;"></mark><mark style="color:$primary;">into</mark> <mark style="color:$primary;"></mark><mark style="color:$primary;">**one coherent, inspectable, portable artifact**</mark><mark style="color:$primary;">.</mark>

{% hint style="success" %}
Think of Stof as a smart, declarative runtime for data workflows, offering code that acts like data, and data that can think.
{% endhint %}

{% hint style="info" %}
Stof works **with** other data formats (both import & export), so you don't have to pick & choose.
{% endhint %}

{% hint style="warning" %}
You can replicate most of what Stof does using JSON + code + libraries; it just takes more effort and lacks formality, unification, etc. (also nightmarish for cross-boundary systems, like APIs, teams, and services).

If you're doing simple config loading or small and static data modeling (and are not already familiar with Stof), it might feel like overkill.
{% endhint %}

## :earth\_americas: Real-World Use-Cases

* **Data-Mesh, Integration, & Orchestration** glue-layer
* **Universal LLM & AI** workflows, tools, & conversational data
* **Smart Configs** with logic, units, and self-validation
* **AI Model Configs** that contain preprocessing logic
* **API Definitions** and transformations with [schemas](book/schemas.md)
* **Self-Describing Datasets** that self-tag and self-validate
* **Data pipelines** with built-in processing and optimization
* **Templates** that generate anything, from YAML to PDFs

## :crystal\_ball: The Difference: It's All Just Data

Unlike traditional programming languages, **everything in Stof is data**:

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

Configurations with expressive logic and validation that won't fail when it matters.

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
