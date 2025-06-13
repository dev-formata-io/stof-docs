---
description: Rename a field using schemafy.
---

# Renaming Fields

If you are aware of the specific field you'd like to rename, "schemafy" offers an interesting and easy way by asking for it in the function as a Box\<str> type.

```rust
schema: {
    #[schema((field: Box<str>) => { field = "desired"; })]
    undesired_name: ""
}

target: {
    undesired_name: "this is cool"
}

#[main]
fn main() {
    self.schema.schemafy(self.target);
    pln(self.target);
}
```

```
> stof run example.stof
{"desired":"this is cool"}
```

However, more generally, you can always ask for the target (and schema) objects, where you have the full capabilities of Stof at your disposal. This enables you to create additional fields, drop fields, rename fields, search for fields, etc.

{% hint style="info" %}
It is easier to read, understand, maintain, and use schemas if you always define the _**desired**_ fields instead of the undesired fields (unlike in the example above). This is more of a preference and might lead to more code in cases, but it's more predictable.
{% endhint %}

{% hint style="info" %}
This code example uses the "search" functionality in the [Object Library](../../reference/libraries/object-library.md) to create a field in a specific location if it does not already exist with the name we are expecting.
{% endhint %}

```rust
schema: {
    #[schema((target: obj, value: unknown): unknown => {
        if (value == null) {
            let search = target.searchDown("othername");
            if (search) return search[0];
        }
        return value;
    })]
    desired: ""
}

target: {
    subobj: {
        another: {
            othername: "yo dude"
        }
    }
}

#[main]
fn main() {
    // remove invalid, remove undefined
    self.schema.schemafy(self.target, true, true);
    pln(self.target);
}
```

```
> stof run example.stof
{"desired":"yo dude"}
```
