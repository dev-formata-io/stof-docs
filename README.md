---
description: >-
  Data and logic have always been separate. That makes things hard. Stof puts
  them together.
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

# 🚀 Stof: Data + Logic

A portable document format where validation, functions, and behavior live alongside the data they belong to, in one document, across any service, language, or runtime.

***

## Why Stof?

### 1. Data that validates and computes itself

**The problem:** Your config file, your schema, and the application code that interprets them are three separate things that all have to stay in sync. When one changes, the others silently break.

**With Stof:** Validation rules and computed values live directly in the data, defined once, enforced everywhere, no separate schema file required.

```rust
#[type]
Server: {
    #[schema((target_val: int): bool => target_val > 1024 && target_val <= 65536)]
    int port: 8080

    #[schema((target_val: str): bool => target_val != "")]
    str! address: "localhost"

    #[schema((target_val: MiB): bool => target_val > 2MB)]
    MiB memory: 500GiB

    fn url() -> str {
        `https://${self.address}:${self.port}`
    }
}

#[main]
fn main() {
    const server = new Server { port: 4000, address: "my-server.com" };
    assert(<Server>.schemafy(server));
    pln(server.url()); // https://my-server.com:4000
}
```

### 2. Prompts, Context, and AI workflows as maintainable data

**The problem:** Prompts are strings. Tool definitions are JSON blobs. Model configs live in application code. As your AI system grows, keeping all of it in sync becomes its own engineering problem.

**With Stof:** Prompts, instructions, and model behavior are structured, composable, and version-controlled as data, not scattered across your codebase as strings.

```rust
background: {
    identity: "You are a helpful assistant."
    
    fn prompt() -> prompt {
        const p = prompt(tag="background");
        p.push(self.identity);
        p.push("Always respond in the user's language.");
        p
    }
}

fn instructions() -> prompt {
    prompt("", tag="instructions",
        prompt("Be concise.", tag="style"),
        prompt("Cite sources when possible.", tag="accuracy")
    )
}

fn system_prompt() -> prompt {
    const p = prompt();
    p.push(self.background.prompt());
    p.push(self.instructions());
    p
}

#[main]
fn main() {
    const str_prompt: str = self.system_prompt();
    pln(str_prompt);
}

/* Output:
<background>
You are a helpful assistant.
Always respond in the user's language.
</background>
<instructions>
<style>Be concise.</style>
<accuracy>Cite sources when possible.</accuracy>
</instructions>
*/
```

### 3. Logic that travels with your data across any boundary

**The problem:** Every time data crosses a service boundary, the logic that operates on it has to be re-implemented, re-validated, or trusted blindly on the other side. The data arrives, but the behavior stays behind.

**With Stof:** A document carries its own enforcement rules, validation, and behavior; parse it anywhere, run it anywhere, no dependencies required.

```rust
// A pricing policy that enforces itself wherever it runs.
// Plans, credits, limits, and validation all in one document.
// See Limitr: https://limitr.dev

Limitr policy: {
    credits: {
        Credit token: {
            label: "Claude Token"
            description: "A currency used to monetize & place usage limits on LLM features"
        }
    }
    plans: {
        Plan pro: {
            label: "Pro Plan"
            price: { amount: 20, suffix: "/month" }
            entitlements: {
                Entitlement ai_chat: {
                    description: "Plan access & usage limit for in-app AI chat feature"
                    Limit limit: { credit: "token", mode: "soft", value: 100_000 }
                }
            }
        }
    }
}
```

{% hint style="info" %}
**Built with Stof:** [Limitr](https://limitr.dev/) is an open source pricing and enforcement engine. The entire policy - plans, credits, limits, validation logic - lives in a single Stof document.
{% endhint %}

***

## Embedded Anywhere

Stof is written in Rust with a slim WASM runtime. Use it from TypeScript, Python, or Rust today.

### TypeScript / JavaScript

```bash
npm i @formata/stof
```

```typescript
import { initStof, stof } from '@formata/stof';
await initStof(); // init wasm once (see readme)

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

### Python

```bash
pip install stof
```

***

## Built with Stof

| Company                                                                                                                                                                                                           | Description                                                      |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| <picture><source srcset=".gitbook/assets/limitr_white_logo.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/limitr_black_logo.png" alt=""></picture>                                           | [Limitr](https://limitr.dev/) - Monetize your AI & SaaS.         |
| <picture><source srcset=".gitbook/assets/Virnika Logo Lockup — Light Varient.svg" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/Virnika Logo Lockup — Dark Varient.svg" alt="Virnika"></picture> | [Virnika](https://www.virnika.ai/) - AI Agents for Restaurants.  |
| <picture><source srcset=".gitbook/assets/kater-logo-dark-full-name-no-bg.svg" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/kater-logo-light-full-name-no-bg.svg" alt=""></picture>              | [Kater](https://www.kater.ai/) - Comprehensive Data & Analytics. |
| Your logo here                                                                                                                                                                                                    | [Discord](https://discord.gg/Up5kxdeXZt)                         |

***

## Get Involved

* [Playground](https://play.stof.dev/) - Try Stof in your browser right now
* [GitHub](https://github.com/dev-formata-io/stof) - Open issues, discussions, and contributions
* [Discord](https://discord.gg/Up5kxdeXZt) - Talk to the team and community
* [info@stof.dev](mailto:info@stof.dev) - Get in touch directly
