---
description: Embedding Stof in JS/TS.
---

# TypeScript

We've provided a [package on JSR](https://jsr.io/@formata/stof) to embed Stof in JavaScript. This can be used in the browser or a JavaScript runtime like Deno or Bun.

{% hint style="warning" %}
Because this package is intended for a wide range of uses, it does not come with any sort of system interface.

The main document interface "Stof" adds a few of the console functions (log, trace, info, error, and warn), however, does not add anything else for you (network access, file system access, etc.).

As such, some of the standard library Stof functions are not available (pln, err, dbg, trace, etc.). The ones that involve printing and the standard system interface that Rust provides.
{% endhint %}

{% hint style="info" %}
In these examples, we're using the [Deno](https://deno.com/) JavaScript runtime.
{% endhint %}

## Hello, World!

Much like other libraries in the JS world, Stof uses a "parse" function to parse a string into a Stof document, with a given format.

As mentioned above, the normal print function "pln" will not work in this WebAssembly context. However, when the Stof document is created using the provided interface, it adds a few "console" library functions (log, trace, info, error, and warn).

```typescript
import { Stof } from 'jsr:@formata/stof';

const doc = await Stof.parse('stof', `
    #[main]
    fn hello() {
        console.log('Hello, world!');
    }
`);

doc.run();
```

```
> deno run --allow-net example.ts
Hello, world!
```
