---
description: Removing fields with schemafy.
---

# Removing Fields

The schemafy function in the [Object Library](../../reference/libraries/object-library.md) gives two parameters for removing fields: one for removing invalid fields (default behavior) and one for removing any fields on a target object that are not defined on the schema object (not enabled by default).

However, one can always use the "removeField" function in the Object Library as well. Maybe for conditional removal or more involved use cases.

## Remove Invalid Fields

Anytime a schema function has a boolean as the return type in the function signature, schemafy will interpret this to be a validation check instead of a new value to set. The default behavior is to remove values that return false (invalid).

{% hint style="info" %}
You can return boolean values as a new, valid value (instead of a valid check) by setting the function signature return type to "unknown" instead of "bool". It is based on the type signature, not the returned value type.
{% endhint %}

```rust
schema: {
    #[schema((value: int): bool => value >= 0 && value <= 200)]
    field: 0
}

target: {
    field: -42
}

#[main]
fn main() {
    assertNot(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{}
```

To disable this functionality:

```rust
schema: {
    #[schema((value: int): bool => value >= 0 && value <= 200)]
    field: 0
}

target: {
    field: -42
}

#[main]
fn main() {
    assertNot(self.schema.schemafy(self.target, false)); // still not valid though
    pln(self.target);
}
```

```
> stof run example.stof
{"field":-42}
```

## Remove Non-Schema Fields

```rust
schema: {
    #[schema((value: int): bool => value >= 0 && value <= 200)]
    field: 0
}

target: {
    field: 42
    
    // additional fields we don't care about
    a: "a"
    b: "b"
    c: "c"
    d: {
        e: "e"
        f: "f"
    }
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target, true, true));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":42}
```

## Conditionally Remove Fields

```typescript
schema: {
    #[schema((target: obj, value: int): bool => {
        drop target.another_field; // or target.removeField("another_field")
        if (value < 20) return false;
        return true;
    })]
    field: 0
}

target: {
    field: 42  // only removed if value is less than 20
    another_field: "will be removed"
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":42}
```
