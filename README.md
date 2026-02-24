---
description: Introduction.
---

# 🚀 Stof: Data + Logic

JSON with functions that you can move around between services/APIs, add/remove/edit in transit, with a slim sandboxed WASM runtime and a clean interop model for host libraries.

{% embed url="https://play.stof.dev" %}

## Use-Cases

* Smart configs with validation and logic
* Data interchange with sandboxed execution
* Prompts as human-readable & maintainable data + code
* AI/LLM workflows and model configs
* Data pipelines with built-in processing
* Integration glue between systems
* Self-describing datasets
* ... basically anywhere data meets logic

## Contributors

{% hint style="info" %}
Check out our [GitHub](https://github.com/dev-formata-io/stof) & [Discord](https://discord.gg/Up5kxdeXZt) server to get involved, or email info@stof.dev.
{% endhint %}

| Company                                                                                                                                                                                                           | Description                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| <picture><source srcset=".gitbook/assets/limitr_white_logo.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/limitr_black_logo.png" alt=""></picture>                                           | [Limitr](https://limitr.dev/) - Complete Monetization for AI & SaaS. |
| <picture><source srcset=".gitbook/assets/Virnika Logo Lockup — Light Varient.svg" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/Virnika Logo Lockup — Dark Varient.svg" alt="Virnika"></picture> | [Virnika](https://www.virnika.ai/) - AI Agents for Restaurants.      |
| <picture><source srcset=".gitbook/assets/kater-logo-dark-full-name-no-bg.svg" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/kater-logo-light-full-name-no-bg.svg" alt=""></picture>              | [Kater](https://www.kater.ai/) - Comprehensive Data & Analytics.     |
| Your logo here                                                                                                                                                                                                    | Reach out on [Discord](https://discord.gg/Up5kxdeXZt)                |

## Examples

{% hint style="success" %}
Check out the [online playground](https://play.stof.dev/) for real examples you can play with right now.
{% endhint %}

### Data Format + Logic

Stof is format-first, similar to JSON, YAML, TOML, etc., with functions that can transform the document they are defined in.

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

## Embedded

Stof is written in Rust and can be embedded today in Python, TypeScript/JavaScript (via WebAssembly), or within your Rust project.

{% hint style="success" %}
Several languages are planned. Please reach out on Discord to get involved.
{% endhint %}

### NPM

`npm i @formata/stof`

```typescript
import { initStof, stof } from '@formata/stof';
await initStof(); // init was once (see readme)

const doc = stof`{
    name: 'world',
    fn hello() -> str { 'Hello, ' + self.name + '!' }
}`;

console.log(await doc.call('hello')); // Hello, world!
```

### Rust

```toml
[dependencies]
stof = "0.9.*"
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

### Python

Stof is available on [PyPi](https://pypi.org/project/stof), just `pip install stof` and `import pystof` module to get started.

```python
from pystof import Doc

STOF = """
#[main]
fn main() {
    const name = Example.name('Stof,', 'with Python');
    pln(`Hello, ${name}!!`)
}
"""

def name(first, last):
    return first + ' ' + last

def main():
    doc = Doc()
    doc.lib('Example', 'name', name)
    doc.parse(STOF)
    doc.run()

if __name__ == "__main__":
    main()

# Output:
# Hello, Stof, with Python!!
```

## Feedback & Community

* Open issues or discussions on [GitHub](https://github.com/dev-formata-io/stof)
* Please join the [Discord](https://discord.gg/Up5kxdeXZt) to get involved and/or discuss Stof
* Email **info@stof.dev** to contact us directly
