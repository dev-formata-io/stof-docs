---
description: >-
  One document, runs anywhere. Send functions over the wire. Documents that
  validate themselves.
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
---

# Stof: Data + Logic

Stof is a portable document format where functions, validation, and behavior live alongside the data they belong to.

It's a superset of JSON - your existing data works as-is. Add logic only where you need it.

***

## Why Stof?

### 1. Interop & interchange data across any boundary

**The problem**: JSON alone is no longer enough - too much ambiguity, too much brittleness, and every API has its own flavor of interchange or DSL. There is no single correct way in distributed systems.

**With Stof**: Stof doesn't try to replace them. It's the layer that works with all of them - parse JSON, YAML, TOML, STOF, binary, etc. into one document, add the logic that belongs with the data (functions), and send it anywhere. Export to any format as needed internally.

```typescript
import { stofAsync } from '@formata/stof';

const doc = await stofAsync`
#[type]
Server: {
    port: 8080
    host: 'localhost'
    secure: false
    MiB memory: 500GiB

    fn url() -> str {
        let url = self.secure ? 'https://' : 'http://';
        url += self.host + ':' + self.port;
        url
    }
}`;

// Parse STOF, JSON, YAML, binary, etc. into the same document
doc.parse(`Server "prod": {
    "host": "prod.example.com",
    "port": 443,
    "secure": true,
    "memory": "2GB"
}`);

console.log(await doc.call('prod.url'));     // https://prod.example.com:443
console.log(doc.get('prod.memory'));         // ~1907 MiB (auto-converted from GB)
```

### 2. Runtime self-assembly

**The problem**: Every API integration is a static contract. When a service modifies its capabilities, every consumer has to update their client code and redeploy. Your system can only do what it shipped with.

**With Stof**: Documents can parse new Stof into themselves at runtime, receiving code over the network and executing it immediately. The document grows while it runs, always sandboxed.

```typescript
import { stofAsync } from '@formata/stof';

const doc = await stofAsync`
    fn loaded() -> str {
        const stof = await Ext.fetch();
        parse(stof, self);
        self.say_hello()
    }
`;

// Bridge to your host environment (full async support)
doc.lib('Ext', 'fetch', async () => {
    return `fn say_hello() -> str { 'Hello, world!' }`;
});

console.log(await doc.call('loaded'));      // Hello, world!
```

### 3. Data that validates and computes itself

**The problem:** Your config file, your schema, and the application code that interprets them are three separate things that all have to stay in sync. When one changes, the others silently break.

**With Stof:** Validation rules and computed values live directly in the data, defined once, enforced everywhere, no separate schema file required.

{% hint style="info" %}
**Built with Stof:** [Limitr](https://limitr.dev/) is an open source pricing and enforcement engine. The entire policy - plans, credits, limits, validation logic - lives in a single Stof document.
{% endhint %}

{% hint style="warning" %}
[Limitr](https://limitr.dev/) policies have built-in validation (they're Stof documents).

This example throws because it's not valid to have a negative time increment: `error: Limit reset increment value must be greater than zero`&#x20;

The [schema](common-patterns/schemas.md) function on the reset\_inc field: `#[schema((target_val: float): bool => target_val > 0)]`
{% endhint %}

```typescript
// A pricing policy that enforces itself wherever it runs.
// Plans, credits, limits, and validation all in one document.
// See Limitr: https://limitr.dev

import { Limitr } from '@formata/limitr';

const yamlPolicy = `
policy:
  credits:
    token:
      description: 'AI token'
  plans:
    growth:
      label: 'Growth Plan'
      entitlements:
        ai_tokens:
          description: 'Included tokens'
          limit:
            credit: token
            value: 100_000
            resets: true
            reset_inc: -1hr
`;

const policy = await Limitr.new(yamlPolicy, 'yaml'); // Stof document

// Usage limits - can this user use this many AI tokens right now?
await policy.allow('user_456', 'ai_tokens', 4200);
```

***

## Use Everywhere

Stof is written in Rust with a slim WASM runtime. Use it from TypeScript, Python, or Rust today.

### TypeScript / JavaScript

```bash
npm i @formata/stof
```

```typescript
import { stofAsync } from '@formata/stof';

const doc = await stofAsync`
    name: 'World'
    fn hello() -> str { 'Hello, ' + self.name + '!' }
`;

console.log(await doc.call('hello')); // Hello, world!
```

### Rust

```toml
[dependencies]
stof = "0.9.*"
```

### Python

```bash
pip install stof
```

***

## Get Involved

* [Playground](https://play.stof.dev/) - Try Stof in your browser right now
* [GitHub](https://github.com/dev-formata-io/stof) - Open issues, discussions, and contributions
* [Discord](https://discord.gg/Up5kxdeXZt) - Talk to the team and community
* [info@stof.dev](mailto:info@stof.dev) - Get in touch directly
