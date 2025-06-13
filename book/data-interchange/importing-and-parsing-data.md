---
description: Using data defined outside of Stof.
---

# Importing and Parsing Data

The vast majority of data in the world is not defined as Stof (shocking, I know). Stof is not _**the**_ standard and never will be. Rather, Stof works with all other data formats so that you can work with data in the form you prefer (or the form it's already in).

Stof [formats.md](../../reference/formats.md "mention") are dynamic and extensible, allowing users to import, export, and parse data in a wide variety of forms.

The simplest way to show this is with the [import statement](../../common-concepts/imports.md). Any format that is loaded into the document can be used to parse data into a document.

## Import Statement

[TOML](https://toml.io/en/) is a popular configuration file format and comes baked into Stof by default. In this example, we'll import a TOML document and add an interface to it with Stof.

{% hint style="info" %}
The same import capability exists when embedding Stof in other host environments and contexts, however, Stof is sandboxed by default and does not have access to importing files unless explicitly enabled.
{% endhint %}

{% tabs %}
{% tab title="interface.stof" %}
Using the same interface we defined in [json-to-stof.md](json-to-stof.md "mention"), we can use the same data in a different form (TOML instead of JSON).

```rust
import "./file.toml";

type Name {
    use: str;
    family: str = "";
    given: vec = [];
    period: obj = null;

    fn name(): str {
        return `${self.given.join(" ")} ${self.family}`;
    }
}

#[main]
fn main() {
    for (Name name in self.name) {
        pln(name.name());
    }
}
```

Using the [Stof CLI](../../reference/cli/), we can run this document, resulting in the same output as before:

```
> stof run ./interface.stof
Peter James Chalmers
Jim
Peter James Windsor
```
{% endtab %}

{% tab title="file.toml" %}
```toml
active = true
id = "example"
resourceType = "Patient"

[[name]]
family = "Chalmers"
given = ["Peter", "James"]
use = "official"

[[name]]
given = ["Jim"]
use = "usual"

[[name]]
family = "Windsor"
given = ["Peter", "James"]
use = "maiden"

[name.period]
end = "2002"
```
{% endtab %}
{% endtabs %}

## Parse Function

Additional files and the import statement are not the only way data can enter a document. The parse function in the [Standard Library](../../reference/libraries/standard-library.md) is a very powerful additional way to do this. Stof can even parse in additional Stof programmatically with the parse function, enabling documents to parse in remote data interfaces.

```typescript
type Name {
    use: str;
    family: str = "";
    given: vec = [];
    period: obj = null;

    fn name(): str {
        return `${self.given.join(" ")} ${self.family}`;
    }
}

#[main]
fn main() {
    let json = '{"active":true,"id":"example","name":[{"family":"Chalmers","given":["Peter","James"],"use":"official"},{"given":["Jim"],"use":"usual"},{"family":"Windsor","given":["Peter","James"],"period":{"end":"2002"},"use":"maiden"}],"resourceType":"Patient"}';
    
    // parses this JSON string into the current object (self) using the 'json' format
    parse(json, 'json');

    for (Name name in name) {
        pln(name.name());
    }
}
```
