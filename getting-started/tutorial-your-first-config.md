---
description: Simple, self-validating config using TypeScript + Stof
---

# Tutorial: Your First Config

In our hypothetical scenario, we have a server that is running a configurable environment for clients. We'll use Stof as the glue between systems to ensure our environment can be safely configured from afar.

## Defining a Simple Config & Testing in TypeScript

```rust
/*!
 * Simple Server Config (Stof).
 */

// semantic versions are a primitive type in Stof
version: 0.1.0-server.example
server: {
    name: "www.example.com:80"
    root_dir: "/etc/httpd"
    ms timeout: 3s
    bool keep_alive: true
    ms keep_alive_timeout: 5s
    GiB ram: 32GiB
    
    // single valid check to start
    fn valid() -> bool {
        self.ram > 2GiB &&
        (!self.keep_alive || self.keep_alive_timeout > 100ms) &&
        self.timeout > 100ms &&
        self.name.len() > 0 &&
        self.root_dir.len() > 0
    }
}
```

We'll use TypeScript and the JSR package to test our valid function:

<pre class="language-typescript"><code class="lang-typescript">import { StofDoc } from "jsr:@formata/stof";
const doc = await StofDoc.new();

// Parse the initial config (could be from file, API, DB, etc.)
doc.parse(`
/*!
 * Simple Server Config (Stof).
 */

version: 0.1.0-server.example
server: {
    name: "www.example.com:80"
    root_dir: "/etc/httpd"
    ms timeout: 3s
    bool keep_alive: true
    ms keep_alive_timeout: 5s
    GiB ram: 32GiB
    
    // single valid check to start
    fn valid() -> bool {
        self.ram > 2GiB &#x26;&#x26;
        (!self.keep_alive || self.keep_alive_timeout > 100ms) &#x26;&#x26;
        self.timeout > 100ms &#x26;&#x26;
        self.name.len() > 0 &#x26;&#x26;
        self.root_dir.len() > 0
    }
}
`);

// Map pln -> console.log &#x26; parse in our main function for testing
doc.lib('Std', 'pln', (...args: unknown[])=>console.log(...args));
doc.parse(`
    #[main]
    fn main() {
<strong>        pln(self.server.valid());
</strong>    }
`);

await doc.run(); // runs all #[main] funcs &#x26; prints "true"
</code></pre>

Run with `deno run` or your preferred JS runtime. Now set the timeout to 0 and run again to see "false".

{% hint style="info" %}
If you don't want to use TypeScript, you can use the [CLI](installation.md) or the [online playground](https://play.stof.dev/) for this tutorial, using the Stof directly (not as a string within TS).
{% endhint %}

## Apply the Server Config

Next, let's add a simple `Server.apply` library function so that our server can apply the desired settings.

<pre class="language-typescript"><code class="lang-typescript">import { StofDoc } from "jsr:@formata/stof";
const doc = await StofDoc.new();

// Parse the initial config
doc.parse(`
/*!
 * Simple Server Config (Stof).
 */

version: 0.1.0-server.example
server: {
    name: "www.example.com:80"
    root_dir: "/etc/httpd"
    ms timeout: 3s
    bool keep_alive: true
    ms keep_alive_timeout: 5s
    GiB ram: 32GiB
    
    // single valid check to start
    fn valid() -> bool {
        self.ram > 2GiB &#x26;&#x26;
        (!self.keep_alive || self.keep_alive_timeout > 100ms) &#x26;&#x26;
        self.timeout > 100ms &#x26;&#x26;
        self.name.len() > 0 &#x26;&#x26;
        self.root_dir.len() > 0
    }
}
`);

// Apply function
<strong>doc.lib('Server', 'apply', (json: string) => console.log(JSON.parse(json)));
</strong>
// Map pln -> console.log &#x26; parse in our main function for testing
doc.lib('Std', 'pln', (...args: unknown[])=>console.log(...args));
doc.parse(`
    #[main]
    fn main() {
<strong>        Server.apply(stringify('json', self.server));
</strong>    }
`);

await doc.run();
</code></pre>

```
> deno run --allow-all config.ts
{
  keep_alive: true,
  keep_alive_timeout: 5000,
  name: "www.example.com:80",
  ram: 32,
  root_dir: "/etc/httpd",
  timeout: 3000
}
```

## Stof Endpoint Handler

Now that we have a very basic setup, let's add a function that mimics an endpoint handler that takes some Stof.

Let's also move the apply logic to an "apply" function within the server's base Stof config, only calling the `Server.apply` lib function when the configuration is valid.

<pre class="language-typescript"><code class="lang-typescript">import { StofDoc } from "jsr:@formata/stof";

const BASE_CONFIG: string = `
/*!
 * Simple Server Config (Stof).
 */

version: 0.1.0-server.example
server: {
    name: "www.example.com:80"
    root_dir: "/etc/httpd"
    ms timeout: 3s
    bool keep_alive: true
    ms keep_alive_timeout: 5s
    GiB ram: 32GiB
    
    // single valid check to start
    fn valid() -> bool {
        self.ram > 2GiB &#x26;&#x26;
        (!self.keep_alive || self.keep_alive_timeout > 100ms) &#x26;&#x26;
        self.timeout > 100ms &#x26;&#x26;
        self.name.len() > 0 &#x26;&#x26;
        self.root_dir.len() > 0
    }

    // apply this config
<strong>    fn apply() -> bool {
</strong>        if (self.valid()) {
            Server.apply(stringify('json', self));
            true
        } else false
    }
}
`;

export async function handler(stof: string): Promise&#x3C;string> {
    const doc = await StofDoc.new();
    
    doc.lib('Std', 'pln', (...args: unknown[])=>console.log(...args));
    doc.lib('Server', 'apply', (json: string) => console.log(JSON.parse(json)));

    doc.parse(BASE_CONFIG);
    doc.parse(stof);
    
    return await doc.run();
}

<strong>await handler(`
</strong>    // additional constraints/APIs from client
    fn set_timeout(time: ms) -> bool {
        if (time > 1s) {
            self.server.timeout = time;
            true
        } else false
    }
    
    #[main]
    fn main() {
        assert(self.set_timeout(5s));
        assert_not(self.set_timeout(-100ms));
<strong>        assert(self.server.apply()); // only applies if valid
</strong>
        self.server.ram = 500MiB; // doesn't fit the valid def
<strong>        assert_not(self.server.apply());
</strong>    }
`);
</code></pre>

```
> deno run --allow-all config.ts
{
  keep_alive: true,
  keep_alive_timeout: 5000,
  name: "www.example.com:80",
  ram: 32,
  root_dir: "/etc/httpd",
  timeout: 5000
}
```

## Stof Schema & Type

Let's use some Stof features to clean this up a bit and formalise our configuration.

Up until now, our workflow is very similar to a config written in JSON, TOML, YAML, etc. Using Stof's type system with a couple [Obj library](../libraries/type-libraries/object-library-obj.md) functions (run & schemafy), we can create self-validating types that can be composed, extended, etc.

{% hint style="info" %}
See [schemas.md](../learn-stof/schemas.md "mention") for a more in-depth look at `Obj.schemafy`, and [object-run.md](../learn-stof/object-run.md "mention") for a more in-depth look at `Obj.run`.
{% endhint %}

<pre class="language-typescript"><code class="lang-typescript">import { StofDoc } from "jsr:@formata/stof";

const BASE_CONFIG: string = `
/*!
 * Simple Server Config (Stof).
 */

version: 0.2.0-server.example

#[type]
Config: {
    #[schema((target_val: str): bool => target_val.len() > 0)]
    str name: "www.example.com:80"

    #[schema((target_val: str): bool => target_val.len() > 0)]
    str root_dir: "/etc/httpd"

    #[schema((target_val: ms): bool => target_val > 100ms)]
    ms timeout: 3s

    #[schema((target: obj, target_val: ms): bool => {
        !target.keep_alive || target_val > 100ms
    })]
    ms keep_alive_timeout: 5s
    bool keep_alive: true
    
    #[schema((target_val: GiB): bool => target_val > 2GiB)]
    GiB ram: 32GiB

    #[run]
    /// Apply this configuration via Obj.schemafy &#x26; Obj.run (see docs)
    fn apply() -> bool {
        // &#x3C;Config> syntax is a path resolution to the "Config" prototype obj
        // "self" is an instance of "Config", not guaranteed to be the prototype
<strong>        if (&#x3C;Config>.schemafy(self)) {
</strong><strong>            Server.apply(stringify('json', self));
</strong>            true
        } else {
<strong>            pln('Invalid server config... ignoring apply');
</strong>            false
        }
    }
}
`;

export async function handler(stof: string): Promise&#x3C;string> {
    const doc = await StofDoc.new();
    
    doc.lib('Std', 'pln', (...args: unknown[])=>console.log(...args));
    doc.lib('Server', 'apply', (json: string) => console.log(JSON.parse(json)));

    doc.parse(BASE_CONFIG);
    doc.parse(stof);
    
    return await doc.run();
}

await handler(`
    // additional constraints/APIs from client
    fn set_timeout(config: Config, time: ms) -> bool {
        if (time > 1s) {
            config.timeout = time;
            true
        } else false
    }
    
    #[main]
    fn main() {
<strong>        const config = new Config {
</strong>            name: "better.example.com"
            ram: 64000MiB
        };
        assert(self.set_timeout(config, 5s));
        assert_not(self.set_timeout(config, -100ms));

<strong>        config.run(); // recursively apply all #[run] funcs, fields, etc.
</strong>
        config.ram = 500MiB;
<strong>        config.run(); // ignores apply
</strong>    }
`);
</code></pre>

```
> deno run --allow-all config.ts
{
  keep_alive: true,
  keep_alive_timeout: 5000,
  name: "better.example.com",
  ram: 62.5,
  root_dir: "/etc/httpd",
  timeout: 5000
}
Invalid server config... ignoring apply
```

## Completed Example

Here is the completed example as just Stof (minus the `Server.apply` TS lib function). Paste into the [online playground](https://play.stof.dev/) and run to try for yourself.

```rust
/*!
 * Simple Server Config (Stof).
 */

version: 0.2.1-server.example

#[type]
Config: {
    #[schema((target_val: str): bool => target_val.len() > 0)]
    str name: "www.example.com:80"

    #[schema((target_val: str): bool => target_val.len() > 0)]
    str root_dir: "/etc/httpd"

    #[schema((target_val: ms): bool => target_val > 100ms)]
    ms timeout: 3s

    #[schema((target: obj, target_val: ms): bool => {
        !target.keep_alive || target_val > 100ms
    })]
    ms keep_alive_timeout: 5s
    bool keep_alive: true
    
    #[schema((target_val: GiB): bool => target_val > 2GiB)]
    GiB ram: 32GiB

    #[run]
    /// Apply this configuration via Obj.schemafy & Obj.run (see docs)
    fn apply() -> bool {
        // <Config> syntax is a path resolution to the "Config" prototype obj
        // "self" is an instance of "Config", not guaranteed to be the prototype
        if (<Config>.schemafy(self)) {
            pln(stringify("toml", self));
            true
        } else {
            pln("Invalid server config... ignoring apply");
            false
        }
    }
}

// additional constraints/APIs from client
fn set_timeout(config: Config, time: ms) -> bool {
    if (time > 1s) {
        config.timeout = time;
        true
    } else false
}

#[main]
fn main() {
    const config = new Config {
        name: "better.example.com"
        ram: 64000MiB
    };
    assert(self.set_timeout(config, 5s));
    assert_not(self.set_timeout(config, -100ms));

    config.run(); // recursively apply all #[run] funcs, fields, etc.

    config.ram = 500MiB;
    config.run(); // ignores apply
}
```

## Next Steps

Now that you have a foundation, play around with a few practical next steps:

* Persistent base configuration Stof document instead of a string in TS
  * Prevents having to parse the base config type, libs, etc. each time
* Use imported TOML, JSON, or YAML for base configuration values
* Create more specific apply functions and/or a complete server Stof API
* Try using the CLI, Rust, or the online playground with these new concepts
