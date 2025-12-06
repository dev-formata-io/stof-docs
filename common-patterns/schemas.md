---
description: Stof objects as schemas.
---

# Schemas

The intersection of functions and data in Stof documents presents an interesting opportunity for a metacode approach to applying and defining schemas.

{% hint style="info" %}
Check out the [object-library-obj.md](../libraries/type-libraries/object-library-obj.md "mention") for more details on `Obj.schemafy(..)`.
{% endhint %}

Schemas in Stof can be used in a lot of use cases: validation, bulk transformations, renaming fields, filtering, etc.

```rust
FirstSchema: {
    #[schema((target_value: str): bool => target_value.len() > 2)]
    /// First name - valid if the length is greater than 2.
    first: "First"
    
    #[schema((
        // can use tuples & lists to create pipelines (&& w/short circuits)
        (target_value: unknown): bool => (typeof target_value) == "str",
        (target_value: str): bool => {"Last", "Example"}.contains(target_value),
    ))]
    /// Last name - valid if a string that is either "Last" or "Example".
    last: "Last"
}

#[main]
fn main() {
    const target = new { first: "Bob", last: "Example" };
    assert(self.FirstSchema.schemafy(target)); // returns true (valid)
    
    target.first = "AJ";
    assert_not(self.FirstSchema.schemafy(target)); // returns false (invalid)
    
    target.first = "Bob";
    target.last = "NotInSet";
    assert_not(self.FirstSchema.schemafy(target)); // invalid

    target.last = 53;
    assert_not(self.FirstSchema.schemafy(target)); // not a str (second fn never gets called)
}
```

{% hint style="info" %}
Schema pipelines, functions, and metadata can come from anywhere in the document and can be composed.
{% endhint %}

## Schema Functions

Functions used in a schema attribute can take many forms - the schemafy operation in the Obj library does its best to fit its behavior to your use case rather than the other way around.

It does this by examining the given argument names and types to associate values with their intended parameters correctly.

### Complete Signature

In the example below, all possible parameters are given.

* `schema: obj` - this is the schema object (Schema in this case).
* `target: obj` - this is the target object (the one being manipulated/validated).
* `field: str` - this is the field name ("field" in this case).
* `schema_val: unknown` - this is the field value on the schema object ("default" in this case).
* `target_val: unknown` - this is the field value on the target object, or null if the field doesn't exist.

```rust
Schema: {
    #[schema((schema: obj, target: obj, field: str, schema_val: unknown, target_value: unknown): bool => true)]
    field: "default"
}
```

{% hint style="info" %}
Any subset or permutation of these parameters will work.
{% endhint %}

{% hint style="info" %}
If the parameter is a value (schema value or target value), add "val" (Ex. "value") to the parameter name and it will always work regardless of type.
{% endhint %}

## Sub Schemas

You might be wondering about recursive schema applications with object fields that are also schemas. If so, there's a shorthand way to apply sub-schemas recursively - the null #\[schema] attribute.

```rust
Schema: {
    #[schema] // if target value is also an object, will call schemafy with them
    sub: {
        #[schema((target_val: int): bool => target_val >= 0)]
        field: 0
    }
}

#[main]
fn main() {
    const target = new {
        sub: new {
            field: -42
        }
    };
    assert_not(self.Schema.schemafy(target));
}
```

## External Field Schema

An object value within the #\[schema(..)] attribute will be interpreted as an external schema to use for that field only.

{% hint style="info" %}
For many external schemas on a field, use a list of schema objects as the #\[schema(\[..])] attribute value.
{% endhint %}

```rust
External: {
    #[schema((target: obj, field: str, schema_val: unknown, target_val: unknown) => {
        if (target_val == null) target.insert(field, schema_val)
    })]
    /// Insert this value if the target field is null or doesn't exist.
    /// Note: functions without a return type are assumed valid.
    field: "value"
}

Schema: {
    #[schema(super.External)]
    field: ""
}

#[main]
fn main() {
    const target = new {};
    assert(self.Schema.schemafy(target));
    assert_eq(target.field, "value");
}
```

## Combinations

Use lists, tuples, and sets to create, combine, and compose schema/validation pipelines.

{% hint style="info" %}
Each schema attribute value is applied recursively, so use lists within lists within lists if you must.
{% endhint %}

```rust
fn is_string(target_val: unknown) -> bool {
    "str" == typeof target_val
}

/// Email string validation pipeline (example).
email_validation: [
    // make sure the value is a string first
    self.is_string,
    
    // if its a string, make sure it has an "@" inside (not at ends)
    (target_val: str): bool => {
        const index = target_val.index_of("@");
        index > 0 && index < target_val.len() - 1
    },
    
    // make sure there is a "." after the @ and before the end
    (target_val: str): bool => {
        const index = target_val.index_of(".");
        const at_index = target_val.index_of("@");
        index > 0 && index < target_val.len() - 1 && index > at_index
    },
]

#[schema(self.email_validation)]
/// Email field that should be validated.
email: "info@example.com"

#[main]
fn main() {
    const target = new { email: "notvalid" };
    assert_not(self.schemafy(target));
    
    target.email = "info@stof.dev";
    assert(self.schemafy(target));
}
```

## Remove Invalid Fields

Schemafy can optionally remove invalid fields for you with `remove_invalid = true`, always modifying the target object to be valid.

```rust
fn is_string(target_val: unknown) -> bool {
    "str" == typeof target_val
}
fn has_at(target_val: str) -> bool {
    const index = target_val.index_of("@");
    index > 0 && index < target_val.len() - 1
}
fn has_dot(target_val: str) -> bool {
    const index = target_val.index_of(".");
    const at_index = target_val.index_of("@");
    index > 0 && index < target_val.len() - 1 && index > at_index
}
fn email_validation() -> list {
    [
        self.is_string,
        self.has_at,
        self.has_dot,
    ]
}

#[schema(self.email_validation())]
email: "info@example.com"

#[main]
fn main() {
    const target = new { email: "missing@dot" };
    assert(self.schemafy(target, remove_invalid = true));
    assert_eq(target.email, null);
}
```

## Remove Not-defined Fields

Schemafy can also optionally remove any fields that are not defined in the schema, with `remove_undefined = true`.

```rust
fn is_string(target_val: unknown) -> bool {
    "str" == typeof target_val
}
fn has_at(target_val: str) -> bool {
    const index = target_val.index_of("@");
    index > 0 && index < target_val.len() - 1
}
fn has_dot(target_val: str) -> bool {
    const index = target_val.index_of(".");
    const at_index = target_val.index_of("@");
    index > 0 && index < target_val.len() - 1 && index > at_index
}
fn email_validation() -> list {
    [
        self.is_string,
        self.has_at,
        self.has_dot,
    ]
}

#[schema(self.email_validation())]
email: "info@example.com"

#[main]
fn main() {
    const target = new { email: "info@stof.dev", additional: 42 };
    assert(self.schemafy(target, remove_undefined = true));
    assert_eq(target.email, "info@stof.dev");
    assert_eq(target.additional, null); // removed
}
```
