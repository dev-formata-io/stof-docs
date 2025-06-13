---
description: Extending Stof with custom libraries.
---

# Extend Stof

Stof is extended with [libraries](../../../reference/libraries/). In a TypeScript context, Stof allows you to call your TypeScript functions directly from Stof using this feature.

## Insert Library

In this example, we add a single function called "add" in a Stof library named "CustomLibrary". To call this function from Stof, reference the library name and function name just like any other library: `CustomLibrary.add(...)`.

{% hint style="info" %}
You may build a library with multiple, separate calls to insertLibrary with the same library name. Functions will be added or replaced if the names collide.
{% endhint %}

```typescript
import { Stof } from 'jsr:@formata/stof';

const doc = await Stof.create();

const magic = 42; // some magic to mix things up
doc.insertLibrary('CustomLibrary', [
    ['add', (a: number, b: number): number => a + b + magic]
]);

doc.importString('stof', `
    #[main]
    fn main() {
        let res = CustomLibrary.add(10, 32);
        console.log(res); // 84
    }
`);
doc.run();
```

```
> deno run --allow-all example.ts
84
```

## Document Context

When Stof calls out to TypeScript, it sets the StofDoc as the current context ("this").

{% hint style="warning" %}
The StofDoc is _**not**_ the same thing as the Stof interface provided by the package. StofDoc is what is held within the Stof interface. Take a look at the [JSR package](https://jsr.io/@formata/stof) for more information and documentation on the StofDoc object.
{% endhint %}

{% hint style="warning" %}
In JavaScript, arrow functions do not have a "this" context, so when you define your library functions, do so with the "function" keyword if you intend to work with the document.
{% endhint %}

```typescript
import { Stof } from 'jsr:@formata/stof';

const doc = await Stof.create();

doc.insertLibrary('Interface', [
    ['insert', function() {
        this.stringImport('stof', `
            fn sayHi() {
                console.log("Was told to say hello...");
            }
        `, 'Interface');
    }]
]);

doc.importString('stof', `
    #[main]
    fn main() {
        Interface.insert();
        Interface.sayHi();
    }
`);
doc.run();
```

```
> deno run --allow-all example.ts
Was told to say hello...
```
