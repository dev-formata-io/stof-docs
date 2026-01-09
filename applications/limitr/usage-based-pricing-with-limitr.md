---
description: Metering usage, enforcing limits, and handling resets.
---

# Usage-Based Pricing with Limitr

{% embed url="https://github.com/dev-formata-io/limitr" %}

{% hint style="info" %}
This example shows how Limitr replaces `usage.ts` and `plan checks` with a single policy document that enforces usage limits locally.

See [combining-seats-usage-and-ai-tokens.md](combining-seats-usage-and-ai-tokens.md "mention") to see how Limitr replaces `tokens.ts` and enforces seats and AI tokens in the same policy document.

See [.](./ "mention") for more general information, and the [GitHub repo](https://github.com/dev-formata-io/limitr) to get involved.
{% endhint %}

## Why Usage-Based Pricing Breaks Down

Usage-based pricing (storage, bandwidth, tokens, compute) usually starts simple:

```ts
if (usage > limit) deny();
```

But quickly becomes complex:

* Units vary (MB, GB, tokens, seconds)
* Limits reset daily, monthly, or hourly
* Free vs paid plans diverge
* Overages must be tracked, not just blocked
* Logic spreads across application code

Limitr moves this logic into a **policy document**, enforced consistently at runtime.

***

## Core Concepts

* **Credits** define the unit of measurement (MB, tokens, seconds)
* **Entitlements** define what can be consumed and how much
* **Meters** track usage per subject
* **Limits** enforce caps and reset behavior
* **Customers** represent users, orgs, API keys, etc.

***

## Example: Storage Usage Limits (TypeScript)

```ts
import { Limitr } from "jsr:@formata/limitr";
import { assert, assertEquals, assertFalse } from 'jsr:@std/assert@^1.0.16';

// Load a policy from wherever you keep policies (DB, API, file, etc.)
// Use TOML, YAML, JSON for simple use cases. For more involved, use Stof.
const policy = await Limitr.new(`
policy:
  credits:
    unit:
      description: "Represents a single MB of usage."
      stof_units: MB  # Stof handles unit parsing & math

  plans:
    free:
      entitlements:
        usage:
          limit:
            credit: unit
            value: 1GB
            resets: true
            reset_inc: 24hr

    pro:
      entitlements:
        usage:
          limit:
            credit: unit
            value: 5GB
            resets: true
            reset_inc: 24hr
`, 'yaml');

// Load customers (users, orgs, Stripe customers, etc.)
await policy.addCustomer('free_user', 'free');
await policy.addCustomer('pro_user', 'pro');

// Meter usage and let Limitr enforce limits
assert(await policy.allow('free_user', 'usage', '20.5MB'));
assert(await policy.allow('free_user', 'usage', 500));

// Inspect meter state
assertEquals(policy.value('free_user', 'usage'), 520.5);
assertEquals(Math.round(policy.balance('free_user', 'usage') as number), 479);
assertEquals(Math.round(policy.limit('free_user', 'usage') as number), 1000);

// Hard limit reached → denied + meter-limit event
assertFalse(await policy.allow('free_user', 'usage', '1GB'));

// Inspect entitlement state (plan or subject level)
console.log(policy.entitlement('free', 'usage'));
```

## What's Happening Here

### Credits

```yaml
credits:
  unit:
    stof_units: MB
```

* Defines the base unit for all calculations
* Accepts human-friendly values (`20.5MB`, `1GB`, `24hr`)
* Uses Stof’s unit system for safe math and conversions

***

### Entitlements & Limits

```yaml
entitlements:
  usage:
    limit:
      credit: unit
      value: 1GB
      resets: true
      reset_inc: 24hr
```

This defines:

* **What** can be consumed (`usage`)
* **How much** (`1GB`)
* **How often it resets** (every 24 hours)

***

## Meters

* Stored per customer per entitlement
* Automatically incremented via `policy.allow(...)`
* Reset automatically based on policy rules
* Fully inspectable and serializable

***

## Automatic Resets

Limitr handles meter resets internally:

* No cron jobs
* No background workers
* No cleanup scripts

Resets are evaluated **at runtime** based on:

* last reset timestamp (per customer meter)
* `reset_inc`
* current time

This makes Limitr safe for:

* embedded apps
* edge environments
* offline execution

***

## Events & Enforcement

When limits are crossed, Limitr emits events:

* `meter-changed`
* `meter-limit`
* `meter-overage` (soft limits)

Your application decides what happens next:

* deny requests
* warn users
* record overages
* trigger billing workflows

Limitr enforces **truth**, not business decisions.

***

## Why This Replaces `usage.ts`

| Problem             | `usage.ts`      | Limitr        |
| ------------------- | --------------- | ------------- |
| Unit parsing        | Custom logic    | Built-in      |
| Resets              | Cron jobs       | Policy-driven |
| Multiple plans      | Branching logic | Declarative   |
| Auditing            | Hard            | Inspectable   |
| Runtime enforcement | Scattered       | Centralized   |

***

## When to Use Usage-Based Limits

Limitr is a strong fit if you are:

* Charging for storage, bandwidth, or compute
* Building AI or API-driven products
* Supporting free tiers or trials
* Running in embedded or self-hosted environments
