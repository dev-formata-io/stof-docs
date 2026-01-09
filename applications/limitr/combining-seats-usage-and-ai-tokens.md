---
description: One policy. Multiple meters. Real-world pricing.
---

# Combining Seats, Usage, and AI Tokens

{% embed url="https://github.com/dev-formata-io/limitr" %}

Modern products rarely price on a single dimension.

A typical AI product might include:

* **Seats** (per org)
* **Usage** (storage, bandwidth, compute)
* **Model tokens** (per-user or per-API key)

Limitr lets you express all of these **in a single policy**, while tracking and enforcing them at the correct scope.

***

## The Core Idea

> **Plans describe entitlements.**\
> **Entitlements define limits.**\
> **Meters track usage.**\
> **Customers own state.**

Each dimension can:

* use its own unit
* reset independently
* apply to different customers
* emit its own events

All without branching logic in application code.

***

## The Policy

This policy defines:

* seat limits per org
* daily usage limits
* per-model token caps
* free vs pro plans

```yml
policy:
  credits:
    unit:
      description: "Represents a single MB of usage."
      stof_units: MB
    seat:
      description: "A single seat in our application."
    token:
      description: "A unit of measurement for model usage."

  plans:
    free:
      entitlements:
        seats:
          limit:
            credit: seat
            value: 3
            increment: 1

        usage:
          limit:
            credit: unit
            value: 1GB
            mode: hard
            resets: true
            reset_inc: 24hr

        davinci_tokens:
          limit:
            credit: token
            value: 500

        curie_tokens:
          limit:
            credit: token
            value: 2000

    pro:
      entitlements:
        seats:
          limit:
            credit: seat
            value: 10
            increment: 1

        usage:
          limit:
            credit: unit
            value: 5GB
            mode: hard
            resets: true
            reset_inc: 24hr

        davinci_tokens:
          limit:
            credit: token
            value: 5000

        curie_tokens:
          limit:
            credit: token
            value: 20000
```

***

## Customers and Scope

In this example:

* **Seats** are tracked per **organization**
* **Usage and tokens** are tracked per **user**
* Users can have **alternate IDs** (Stripe customer, API key, etc.)

```ts
import { Limitr } from "jsr:@formata/limitr";
import { assert, assertEquals, assertFalse } from 'jsr:@std/assert@^1.0.16';

// load a policy from wherever you keep policies (db, local string, server, etc.)
const policy = await Limitr.new(`# yaml from above`, 'yaml');

// Create an org customer with free plan
await policy.addCustomer('free_org', 'free', 'org', 'Free Org');

// Create a user linked to the org plan with an additional, alternate ID
await policy.addCustomer(
  'free_user',
  '',
  undefined,
  undefined,
  'free_org',
  ['cus_alt']
);
```

***

## Seat Enforcement (Org-Level)

```ts
assert(await policy.increment(policy.subjectOrg('free_user') as string, 'seats'));
assert(await policy.allow(policy.subjectOrg('cus_alt') as string, 'seats', 2));

assertFalse(
  await policy.increment('free_org', 'seats')
); // 4th seat denied
```

**What’s happening:**

* Seats are enforced on the org customer
* Any user tied to the org consumes from the same meter
* Limitr prevents exceeding the plan limit

***

## AI Token Enforcement (User-Level)

```ts
assert(await policy.allow('free_user', 'davinci_tokens', 300));
assertFalse(await policy.allow('free_user', 'davinci_tokens', 201)); // over limit by 1

assert(await policy.allow('free_user', 'curie_tokens', 1000));
assertFalse(await policy.allow('free_user', 'curie_tokens', 1001)); // over limit by 1
```

**Key detail:**

* Each model has its own entitlement
* Tokens are tracked independently
* Limits are enforced deterministically

***

## Usage Enforcement with Units

```ts
assert(await policy.allow('cus_alt', 'usage', '20MB'));
assertEquals(policy.value('free_user', 'usage') as number, 20);

assertFalse(
  await policy.allow('free_user', 'usage', '1GB')
); // exceeds daily cap
```

* Units are parsed automatically
* Internal state is normalized (MB)
* Limits are enforced consistently

***

## Plan Changes Without Redeploy

```ts
assert(await policy.setCustomerPlan('free_org', 'pro'));

assert(await policy.allow('cus_alt', 'curie_tokens', 12200));
assertEquals(policy.value('cus_alt', 'curie_tokens'), 13200);

assertFalse(
  await policy.allow('cus_alt', 'curie_tokens', 7000)
);
```

Changing the plan immediately affects enforcement — no code changes, no redeploy. Alternatively, just change the plan field in the customer (if referencing a remote customer record).

***

## Persisting State

```ts
console.log(policy.customers());
```

All customer state (meters, balances, resets, plans) can be:

* serialized
* stored in a database
* synced across systems

***

## Why This Matters

This single policy replaces:

* `limits.ts`
* `usage.ts`
* `tokens.ts`
* ad-hoc plan checks
* fragile billing conditionals

Limitr becomes the **source of truth for monetization enforcement**.

***

## Mental Model

> **Your app asks:** “Can I do this?”\
> **Limitr answers:** “Yes, no, or record an overage — and here’s why.”

***

## Complete Example Code

```ts
import { Limitr } from "jsr:@formata/limitr";
import { assert, assertEquals, assertFalse } from 'jsr:@std/assert@^1.0.16';

// load a policy from wherever you keep policies (db, local string, server, etc.)
const policy = await Limitr.new(`
policy:
  credits:
    unit:
      description: "Represents a single MB of usage."
      stof_units: MB # magic happens here (stof units)
    seat:
      description: "A single seat in our application."
    token:
      description: "A unit of measurement for model usage."
  plans:
    free:
      entitlements:
        seats:
          limit:
            credit: seat
            value: 3 # maximum of 3 seats in the free plan
            increment: 1 # increment 1 seat at a time
        usage:
          limit:
            credit: unit
            value: 1GB
            mode: hard # soft would allow overages
            resets: true
            reset_inc: 24hr # automatically resets meter after this long (stof units)
        davinci_tokens:
          limit:
            credit: token
            value: 500
        curie_tokens:
          limit:
            credit: token
            value: 2000
    pro:
      entitlements:
        seats:
          limit:
            credit: seat
            value: 10 # maximum of 10 seats in the pro plan
            increment: 1 # increment 1 seat at a time
        usage:
          limit:
            credit: unit
            value: 5GB
            mode: hard # soft would allow overages
            resets: true
            reset_inc: 24hr # automatically resets meter after this long (stof units)
        davinci_tokens:
          limit:
            credit: token
            value: 5000
        curie_tokens:
          limit:
            credit: token
            value: 20000
`, 'yaml');

// Load customers (users, orgs, Stripe customers, etc.)
// First lets create an org customer (for seats and anything tracked per org)
await policy.addCustomer('free_org', 'free', 'org', 'Free Org');

// Now lets create a user test customer linked to the org plan, with an additional ID (Stripe customer ID, app ID, API key, etc.)
await policy.addCustomer('free_user', '', undefined, undefined, 'free_org', ['cus_alt']);

// Now we're all set to track things for the org and user together!
// Lets increment a few seats on the org first.
assert(await policy.increment(policy.subjectOrg('free_user') as string, 'seats'));
assert(await policy.allow(policy.subjectOrg('cus_alt') as string, 'seats', 2));
assertFalse(await policy.increment('free_org', 'seats')); // cannot add a 4th seat to the org

// Lets track model tokens individually per user
assert(await policy.allow('free_user', 'davinci_tokens', 300));
assertFalse(await policy.allow('free_user', 'davinci_tokens', 201)); // over by 1
assert(await policy.allow('free_user', 'curie_tokens', 1000));
assertFalse(await policy.allow('free_user', 'curie_tokens', 1001)); // over by 1

// Usage is tracked per user as well, and we can use any alt ID for the user
assert(await policy.allow('cus_alt', 'usage', 20 + 'MB'));
assertEquals(policy.value('free_user', 'usage') as number, 20); // always units of credit (MB)
assertFalse(await policy.allow('free_user', 'usage', '1GB')); // max of 1GB, so over by 20MB

// Now lets switch the plan for the org
assert(await policy.setCustomerPlan('free_org', 'pro'));
assert(await policy.allow('cus_alt', 'curie_tokens', 12200));
assertEquals(policy.value('cus_alt', 'curie_tokens') as number, 13200); // already had 1k from above
assertFalse(await policy.allow('cus_alt', 'curie_tokens', 7000)); // would be over by 200

// Now lets store our customers (entire state info)
console.log(policy.customers());
```

```bash
> deno run example.ts
{
  free_org: {
    id: "free_org",
    plan: "pro",
    type: "org",
    label: "Free Org",
    org: null,
    alt_ids: [],
    meters: { seats: { credit: "seat", reset: null, value: 3 } }
  },
  free_user: {
    id: "free_user",
    plan: "",
    type: "user",
    label: "User",
    org: "free_org",
    alt_ids: [ "cus_alt" ],
    meters: {
      davinci_tokens: { credit: "token", reset: null, value: 300 },
      curie_tokens: { credit: "token", reset: null, value: 13200 },
      usage: { credit: "unit", reset: 1767806314778, value: 20 }
    }
  }
}
```

## Getting Started <a href="#getting-started" id="getting-started"></a>

1. Define your credits for tokens and entitlements for each plan
2. Attach customers (users/orgs/api keys) to plans
3. Increment meters as API calls happen
4. Listen for events to handle overages or billing

Limitr lets you start simple (free vs pro) and scale up to complex AI pricing without touching your application code.

{% hint style="success" %}
Star the GitHub repo, create a bug/ticket, reach out on Discord, and get involved.

I use Limitr with Stripe, so if you want more details on this, let me know (cj@stof.dev).
{% endhint %}

{% embed url="https://github.com/dev-formata-io/limitr" %}
