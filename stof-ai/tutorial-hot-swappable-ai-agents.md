---
description: Stof as portable & flexible agent behaviors
---

# Tutorial: Hot-Swappable AI Agents

* Store agent behaviors in your database as Stof artifacts
* Update agent logic without redeploying
* A/B test different agent strategies by swapping workflows
* Roll back bad changes instantly

## Intro

Executable data opens many doors. AI is one of the most obvious examples because there are numerous challenges that current languages and frameworks were not designed to address.

When it comes to AI agents, Stof can help as:

* Portable and self-describing agent behavior
* Dynamic contexts & model selection
* Prompt development, management, and composition
* Tools as data, with control, portability, and flexibility

## Setup & Test

We'll use TypeScript with Stof for this example, showing a customer support AI agent where you can update the agent's behavior in real-time by swapping Stof workflow artifacts stored in your database.

### Anthropic Stof API

Here is a very simple Stof <-> Anthropic API. We'll be using Stof as the complete interface for AI.

This will be included in our TypeScript example as a single string variable called "ANTHROPIC\_STOF\_API", but will include the value ".. See Setup .." as a placeholder to keep our examples neat and readable.

```rust
#[type]
Anthropic: {
    #[static]
    /// Create an anthropic message from a prompt.
    fn prompt_to_message(pr: prompt | str, arena?: obj) -> obj {
        new {
            role: "user",
            content: [new {
                type: "text",
                text: pr as str
            }]
        } on arena
    }

    #[static]
    /// Send a prompt to Anthropic and get a str response.
    async fn send(api_key: str, pr: prompt | str, system?: prompt | str) -> str {
        const arena = new {};
        const message = self.prompt_to_message(pr, arena);
        const body = new {
            model: "claude-3-5-haiku-latest",
            max_tokens: 3_000,
            messages: [message],
            temperature: 0.2,
            system: (system as str) ?? "",
        } on arena;
        const headers = {
            "x-api-key": api_key ?? "",
            "anthropic-version": "2023-06-01",
            "content-type": "application/json"
        };

        const resp = await Http.fetch(
            url = "https://api.anthropic.com/v1/messages",
            method = "post",
            body = stringify("json", body),
            headers = headers
        );
        if (!Http.success(resp)) {
            drop(arena);
            return null;
        }

        resp.insert("content_type", "json");
        const conv = Http.parse(resp, new {} on arena);
        let result = "";
        if ("list" == typeof conv.content) {
            ^content_loop for (const content in conv.content) {
                if (content.type == "text") {
                    result = content.text;
                    break ^content_loop;
                }
            }
        }
        drop(arena);
        result
    }
}
```

### Create Stof Document

We'll create a Stof document in TypeScript with the following function:

```typescript
import { StofDoc } from "jsr:@formata/stof";

const ANTHROPIC_STOF_API: string = `
.. See Setup: Anthropic Stof API ..
`;

// Create the base Stof document with Anthropic, fetch, & pln enabled.
export async function createStofDoc(): Promise<StofDoc> {
    const doc = await StofDoc.new();
    
    doc.lib('Std', 'pln', (... vars: unknown[]) => console.log(...vars));
    doc.lib('Http', 'fetch', async (
        url: string,
        method: string = 'GET',
        body: string | Uint8Array | null = null,
        headers: Map<string, string> = new Map()): Promise<Map<string, unknown>> => {
        const response = await fetch(url, {
            method,
            body: body ?? undefined,
            headers,
        });
        const result = new Map<string, unknown>();
        result.set('status', response.status);
        result.set('ok', response.ok);
        result.set('headers', new Map(response.headers));
        result.set('content_type', response.headers.get('content-type') ?? response.headers.get('Content-Type') ?? 'text/plain');
        result.set('bytes', await response.bytes());
        return result;
    }, true);

    doc.parse(ANTHROPIC_STOF_API);
    return doc;
}
```

### Agent & Handler

We'll use the following two functions as a simulated handler and Stof agent interaction. In the real world, this would be more fleshed out, involving a Stof pipeline (typically using [Run](../common-patterns/object-run.md) and other Stof features).

{% hint style="info" %}
For getAgent, you'd most likely store a Stof API in your DB and supply a function that uses that API. The entire agent API would then be hot-swappable.
{% endhint %}

```typescript
// Get an agent API from our (simulated) database as a Stof string.
export async function getAgent(func: string, message: string): Promise<string> {
    // just a test call for now with our agent code
    return `
        fn ${func}() -> str {
            return await <Anthropic>.send('${ANTHROPIC_API_KEY}', r#"${message}"#);
        }
    `;
}

// Handle a customer message with our agent.
export async function handleCustomerMessage(message: string): Promise<string> {
    const doc = await createStofDoc();
    
    const func = 'agent_func';
    const agentStof = await getAgent(func, message);
    doc.parse(agentStof);
    
    const response = await doc.call(func);
    return response as string;
}
```

### Completed Template & Test

Here is the full TypeScript template we will use in its entirety:

```typescript
import { StofDoc } from "jsr:@formata/stof";

const ANTHROPIC_API_KEY: string = 'YOUR API KEY HERE';
const ANTHROPIC_STOF_API: string = `
#[type]
Anthropic: {
    #[static]
    /// Create an anthropic message from a prompt.
    fn prompt_to_message(pr: prompt | str, arena?: obj) -> obj {
        new {
            role: 'user',
            content: [new {
                type: 'text',
                text: pr as str
            }]
        } on arena
    }

    #[static]
    /// Send a prompt to Anthropic and get a str response.
    async fn send(api_key: str, pr: prompt | str, system?: prompt | str) -> str {
        const arena = new {};
        const message = self.prompt_to_message(pr, arena);
        const body = new {
            model: 'claude-3-5-haiku-latest',
            max_tokens: 3_000,
            messages: [message],
            temperature: 0.2,
            system: (system as str) ?? '',
        } on arena;
        const headers = {
            'x-api-key': api_key ?? '',
            'anthropic-version': '2023-06-01',
            'content-type': 'application/json'
        };

        const resp = await Http.fetch(
            url = 'https://api.anthropic.com/v1/messages',
            method = 'post',
            body = stringify('json', body),
            headers = headers
        );
        if (!Http.success(resp)) {
            drop(arena);
            return null;
        }

        resp.insert('content_type', 'json');
        const conv = Http.parse(resp, new {} on arena);
        let result = '';
        if ('list' == typeof conv.content) {
            ^content_loop for (const content in conv.content) {
                if (content.type == 'text') {
                    result = content.text;
                    break ^content_loop;
                }
            }
        }
        drop(arena);
        result
    }
}
`;

// Create the base Stof document.
export async function createStofDoc(): Promise<StofDoc> {
    const doc = await StofDoc.new();
    
    doc.lib('Std', 'pln', (... vars: unknown[]) => console.log(...vars));
    doc.lib('Http', 'fetch', async (
        url: string,
        method: string = 'GET',
        body: string | Uint8Array | null = null,
        headers: Map<string, string> = new Map()): Promise<Map<string, unknown>> => {
        const response = await fetch(url, {
            method,
            body: body ?? undefined,
            headers,
        });
        const result = new Map<string, unknown>();
        result.set('status', response.status);
        result.set('ok', response.ok);
        result.set('headers', new Map(response.headers));
        result.set('content_type', response.headers.get('content-type') ?? response.headers.get('Content-Type') ?? 'text/plain');
        result.set('bytes', await response.bytes());
        return result;
    }, true);

    doc.parse(ANTHROPIC_STOF_API);
    return doc;
}

// Get an agent API from our (simulated) database as a Stof string.
export async function getAgent(func: string, message: string): Promise<string> {
    // just a test call for now with our agent code
    return `
        fn ${func}() -> str {
            return await <Anthropic>.send('${ANTHROPIC_API_KEY}', r#"${message}"#);
        }
    `;
}

// Handle a customer message with our agent.
export async function handleCustomerMessage(message: string): Promise<string> {
    const doc = await createStofDoc();
    
    const func = 'agent_func';
    const agentStof = await getAgent(func, message);
    doc.parse(agentStof);
    
    const response = await doc.call(func);
    return response as string;
}


// Test it out.
console.log(await handleCustomerMessage('How tall are penguins on average?'));
```

#### Output

If you input your Anthropic API key and run `deno run --allow-all example.ts`, you'll see something like:

```
> deno run --allow-all example.ts
The height of penguins varies depending on the species. Here are some average heights for a few common penguin species:

1. Emperor Penguin: Tallest penguin species, standing around 3.8 feet (1.2 meters) tall

2. King Penguin: About 3 feet (0.9 meters) tall

3. Gentoo Penguin: Around 2.5 feet (0.75 meters) tall

4. Adelie Penguin: Approximately 2 feet (0.6 meters) tall

5. Little (Blue) Penguin: The smallest species, standing about 16 inches (40 cm) tall

The average height across all penguin species is roughly 2 to 3 feet (0.6 to 0.9 meters).
```

## Hot Swap With Flag

We'll focus now just on the getAgent function, where our hot swap example resides. In real life, you have nearly unlimited options for what you could do here:

* Access a DB
* Call an API
* Use feature flags (like our example)
* Hardcode your agent
* A dynamic or user-provided context & prompts
* ... sky's the limit really

### Updated GetAgent + Flag

```typescript
export enum Mode {
    Cautious,
    Proactive,
}
let agentMode = Mode.Cautious;

// Get an agent API from our (simulated) database as a Stof string.
export async function getAgent(func: string, message: string): Promise<string> {
    // Set the system prompt based on a feature flag
    let system = '';
    switch (agentMode) {
        case Mode.Cautious: {
            system = `You are a cautious support agent. Always ask for clarification before taking action.`;
            break;
        }
        case Mode.Proactive: {
            system = `You are a proactive support agent. Anticipate needs and aggressively offer solutions immediately.`;
            break;
        }
    }

    // Simulate a pipeline that gathers account info and creates a prompt(s) with context
    // Remember, the "prompt" primitive type is a tree of optionally tagged/structured strings
    const messagePrompt = `prompt('', '',
        prompt('Online Store T-Shirt order #34567 with the wrong size', 'context'),
        prompt(r#"${message}"#, 'user'))`;

    // Return a function that does the entire agent interaction
    return `
        fn ${func}() -> str {
            return await <Anthropic>.send('${ANTHROPIC_API_KEY}', ${messagePrompt}, r#"${system}"#);
        }
    `;
}
```

### Execution

Let's test this out with a quick user message of "I'm having issues with my order" with the two agent modes.

```typescript
agentMode = Mode.Cautious;
console.log('Cautious (V1) Agent:');
console.log(await handleCustomerMessage("I'm having issues with my order."));

agentMode = Mode.Proactive;
console.log('\nProactive (V2) Agent:');
console.log(await handleCustomerMessage("I'm having issues with my order."));
```

#### Output

```
> deno run --allow-all example.ts
Cautious (V1) Agent:
I understand you're experiencing a problem with your order. Could you please provide more specific details about the issue you're having with the T-shirt order #34567? What seems to be wrong with the size, and what would you like me to help you with?

Proactive (V2) Agent:
I understand you're experiencing a problem with your T-shirt order #34567. I'm here to help resolve this right away. You mentioned it's the wrong size - would you like me to:

1. Process an immediate exchange for the correct size
2. Arrange a free return and replacement
3. Provide a refund

Which option would work best for you? I want to make this as smooth as possible and get you the right shirt quickly.
```

## Further Improvements & Ideas

Stof provides a lot of flexibility in your implementation, including importing/managing customer records and data, validations, schemas, etc.

Try importing customer records from JSON/TS that fit a Stof type, then alter the agent's behavior, customizing it to the customer dynamically.

{% hint style="info" %}
Organize data within your Stof document by root object or by sub-objects, where you can be sure imports/APIs don't collide (or do so gracefully).
{% endhint %}

```rust
// example loaded customer Stof data
#[type]
Customer: {
    str tier: "premium"
    float sentiment: 0.6
    
    // Customer Stof API for managing records
    // Import/Export from JSON, TOML, YAML, etc. as needed
    fn set_sentiment(value: float, min: float = 0, max: float = 1) {
        if (max == min) throw("Max cannot equal min value");
        value = min(max(value, min), max);
        self.sentiment = (value - min) / (max - min);
    }
}

// Generated per customer!
fn handle_message(user_msg: str, customer: Customer) -> str {
  const customer_tier = customer.tier; // "premium" | "standard"
  const customer_sentiment = customer.sentiment;
  
  // Logic adapts based on who they are
  const temperature = customer_tier == "premium" ? 0.8 : 0.5;
  const system_prompt = customer_sentiment < 0.3 ? "Be extra empathetic and offer proactive help" : "Be efficient and solution-focused";
  
  // ... rest of implementation
}
```

## Takeaways

{% hint style="info" %}
In the real world, you can do a lot to make the Stof/TS boundary more bulletproof. This includes limiting the amount of programmatic Stof being generated from TS with a stable internal Stof API, validating inputs, a tested AI API with retries, etc.
{% endhint %}

This is a simple example to serve as a foundation for experimentation with Stof and AI. With Stof's portability and flexibility, you can:

* Store agent behaviors in your database as Stof artifacts
* Update agent logic without redeploying
* A/B test different agent strategies by swapping workflows
* Roll back bad changes instantly

In addition, Stof can greatly simplify your application logic, especially in cases where your systems are distributed.
