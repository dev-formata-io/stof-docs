---
description: Enabling Stof import statements.
---

# Import Statement

Stof import statements use the [file system library](../../../reference/cli/cli-libraries/filesystem-library.md). Although this library is not enabled by default in a WebAssembly context, we can add it ourselves.

{% hint style="warning" %}
This is for non-browser JavaScript, where system access is available.
{% endhint %}

{% hint style="info" %}
See [imports.md](../../../common-concepts/imports.md "mention") for more information on the Stof import statement.
{% endhint %}

## Text Files

For the Stof import statement, we need the 'fs.read(..)' function. This function is used internally by the loaded formats in the document.

{% hint style="info" %}
Note that if you are aware of the file to be included, it's probably easier to use the "importString(..)" function after reading the file into a string yourself.
{% endhint %}

{% tabs %}
{% tab title="example.ts" %}
```typescript
import { Stof } from 'jsr:@formata/stof';

const doc = await Stof.create();

doc.insertLibrary('fs', [
    ['read', (path: string): string => Deno.readTextFileSync(path)]
]);
doc.importString('stof', `
    import 'import.toml'; // Calls fs.read('import.toml') in the background...

    #[main]
    fn main() {
        console.log('Hello, ' + self.name);
    }
`);

doc.run();
```
{% endtab %}

{% tab title="import.toml" %}
```toml
name = "Bob Smith"
age = 44
```
{% endtab %}
{% endtabs %}

```
> deno run --allow-all example.ts
Hello, Bob Smith
```
