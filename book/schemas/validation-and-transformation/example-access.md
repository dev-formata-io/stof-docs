---
description: Example for accessing and using a field with schemafy.
---

# Example Access

This is a more realistic example, showing nested pipelines for validity and a more realistic conversion for ensuring the proper units on a field.

{% hint style="info" %}
In a production environment, you'll want a more robust access control layer (obviously). This could involve additional host [libraries](../../../reference/libraries/) or additional data parsed into the document at the appropriate times.
{% endhint %}

```typescript
access: {
    #[private]
    #[readonly]
    layer: "everything"

    #[private]
    #[readonly]
    token: "abcdefghi"

    // use this in all schema attributes that require access
    validation_pipeline: [
        (target: obj): bool => self.hasAccess(target.token),
        (target: obj): bool => target.layer.or("everything") == self.layer,
    ]

    fn hasAccess(token: str): bool {
        return token == self.token;
    }
}

schema: {
    #[schema([
        // stop here if the target object doesn't have access
        // nested pipelines will all work, no matter how deep
        super.access.validation_pipeline,

        // use this schema's value as the default if one doesn't exist
        (schema: obj, value: unknown): unknown => {
            if (value == null) return schema.ttl;
            return value;
        },

        // ensure the value is a number and that it has units of time
        (value: unknown): bool => isNumber(value) && value.isTime(),

        // convert all times to units of seconds
        (value: float): s => value,
    ])]
    ttl: 3600s
}

#[test]
fn valid_token() {
    let valid = new {
        token: "abcdefghi"
    };
    assert(self.schema.schemafy(valid, true, true));
    pln(valid);
}

#[test]
fn valid_value() {
    let valid = new {
        token: "abcdefghi"
        ttl: 129444ms
    };
    assert(self.schema.schemafy(valid, true, true));
    pln(valid);
}

#[test]
fn invalid_layer() {
    let invalid = new {
        layer: "unknown" // unknown/invalid access layer
        token: "abcdefghi"
        ttl: 1800s
    };
    assertNot(self.schema.schemafy(invalid, true, true));
    pln(invalid);
}

#[test]
fn invalid_token() {
    let invalid = new {
        token: "abc" // wrong token
    };
    assertNot(self.schema.schemafy(invalid, true, true));
    pln(invalid);
}

#[test]
fn invalid_time() {
    let invalid = new {
        token: "abcdefghi"
        ttl: 33kg // mass instead of time
    };
    assertNot(self.schema.schemafy(invalid, true, true));
    pln(invalid);
}
```

```
> stof test example.stof
running 5 Stof tests
{"ttl":3600.0}
test root valid_token ... ok
{"ttl":129.444}
test root valid_value ... ok
{}
test root invalid_layer ... ok
{}
test root invalid_token ... ok
{}
test root invalid_time ... ok

test result: ok. 5 passed; 0 failed; finished in 0s
```
