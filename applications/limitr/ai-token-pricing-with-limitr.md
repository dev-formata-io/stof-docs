---
description: Enforcing usage-based limits for AI products, built on Limitr.
---

# AI Token Pricing with Limitr

{% embed url="https://github.com/dev-formata-io/limitr" %}

AI products charge per API call, token, or compute unit. Problems with `limits.ts`:

* Hard-coded thresholds that need constant redeploys
* Special cases for paid/free users, free tiers, or trial periods
* Complexity when multiple models, token types, or rate-limits exist
* No easy audit for usage overages
* Hard to integrate with billing or monitoring

AI token pricing is **dynamic**. Limits and usage tracking must evolve without touching the app code.

## How Limitr Solves It

Limitr separates **usage policy** from **application logic**.

* Define **credits** for tokens
* Attach **entitlements** to plans
* Track **meters** per customer
* Enforce **limits** at runtime
* Emit **events** when limits are reached

Benefits:

* Update token limits instantly without redeploying
* Keep usage state explicit and auditable
* Integrate easily with billing systems
* Handle overages with soft or hard enforcement

***

## Example: Token-Based Limits

```yaml
policy:
  credits:
    token:
      description: "A single AI token for model usage"
  plans:
    free:
      entitlements:
        tokens:
          limit:
            credit: token
            value: 1000
            mode: soft  # allows overage events
    pro:
      entitlements:
        tokens:
          limit:
            credit: token
            value: 10000
```

* Free users have a **soft limit** of 1,000 tokens per month (overage triggers events, but may be allowed)
* Pro users have a **hard limit** of 10,000 tokens per month

### Enforcing Token Usage

```ts
import { Limitr } from "jsr:@formata/limitr";

// create a limitr policy (from db, server, local string, etc.)
const policy = await Limitr.new(`# policy goes here`, 'yaml');

// create test customers (or load some saved ones, Stripe ones, etc.)
await policy.addCustomer('free_user', 'free');
await policy.addCustomer('pro_user', 'pro');

// add event handlers either in doc (its just stof) or to policy as pre-defined (App.meter_overage, etc..)
policy.doc.lib('App', 'meter_overage', (json: string) => { const r = JSON.parse(json); console.log('Overage customer: ', r.customer, r.remaining); });
policy.doc.lib('Custom', 'example_event_handler', (user: string, remaining: number) => { console.log("firing a custom event handler for", user, remaining); });
policy.doc.parse(`
    #[meter-overage]
    fn meter_over_limit(val: obj) {
        ?Custom.example_event_handler(val.customer.id, val.remaining);
    }
`);

// determin allowed or not while metering usage
const allowed = await policy.allow('free_user', 'tokens', 1400);
if (!allowed) console.log('Free user is not allowed');
```

```bash
> deno run example.ts
firing a custom event handler for free_user -400
Overage customer:  {
  id: "free_user",
  plan: "free",
  type: "user",
  label: "User",
  org: null,
  alt_ids: [],
  meters: { tokens: { credit: "token", reset: null, value: 1400 } }
} -400
```

* `policy.allow` automatically updates the **meter** if a hard limit hasn't been reached
* Events (`meter-limit`, `meter-overage`, etc.) fire on threshold violations
* The app decides how to respond (block, warn, bill, or notify)

***

## Metering Multiple Models

Many AI products have multiple models with different token costs:

```yaml
policy:
  credits:
    token:
      description: "AI tokens"

  plans:
    free:
      entitlements:
        davinci_tokens:
          limit:
            credit: token
            value: 500
            mode: soft
        curie_tokens:
          limit:
            credit: token
            value: 2000
            mode: soft
```

* Each entitlement tracks a specific model or token type
* Meters are stored per customer
* Overages emit events per entitlement, giving granular insight

***

## Soft vs Hard Limits

* **Soft Limit**: Usage above the limit triggers events but may be allowed
* **Hard Limit**: Usage above the limit is blocked automatically

This allows AI products to **warn users** or **record overages** before enforcing strict caps.

***

## Why Limitr Beats `limits.ts` for AI

| Problem                       | `limits.ts`             | Limitr                             |
| ----------------------------- | ----------------------- | ---------------------------------- |
| Frequent token limit changes  | Requires redeploy       | Update policy without redeploy     |
| Multiple models / token types | Requires branching      | Each entitlement is explicit       |
| Auditing usage                | Hard to track           | Meters are inspectable & auditable |
| Event-driven overages         | Must write custom logic | Built-in event system              |
| Integration with billing      | Custom code             | Subscribe to events, no coupling   |

***

## Getting Started

1. Define your credits for tokens and entitlements for each plan
2. Attach customers (users/orgs/api keys) to plans
3. Increment meters as API calls happen
4. Listen for events to handle overages or billing

Limitr lets you start simple (free vs pro) and scale up to complex AI pricing without touching your application code.
