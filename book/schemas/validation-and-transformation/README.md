---
description: Validating and transforming values with schemafy.
---

# Validation & Transformation

For each function passed into the #\[schema(...)] [attribute](../../../reference/libraries/object-library.md), it can either validate or transform a value. Validation occurs when the function signature's return type is a bool. Otherwise, Stof will instead set the new value (if it is not null).

## Validation

The purpose of validation is to determine whether the schema is adhered to by the target object. Schemafy offers the option (enabled by default) to remove any invalid fields.

This can be helpful for access control or in other scenarios when you want to ensure only valid data is kept.

```typescript
schema: {
    #[schema((value: str): bool => set("a", "b", "c", "d").contains(value))]
    field: "a"
}

target: {
    field: "c"
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);

    self.target.field = "e";
    assertNot(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":"c"}
{}
```

## Transformation

Transformation functions do not determine the validity of a field, rather, they transform the value of the field or potentially prompt the creation of a new field.

```typescript
schema: {
    #[schema((value: str): str => {
        // could be another field on the target or schema or somewhere else...
        let valid = set("a", "b", "c", "d");
        if (!valid.contains(value)) return valid.last();
        return value;
    })]
    field: "a"
}

target: {
    field: "c"
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);

    self.target.field = "e";
    assert(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":"c"}
{"field":"d"}
```

### Boolean Value Transformation (would normally be validation)

```typescript
schema: {
    // not a boolean return type in the function signature
    #[schema((value: bool): unknown => {
        return true;
    })]
    field: true
}

target: {
    field: false
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":true}
```

### New Field

Fields that are not present on the target object but present in the schema will have a value of "null" when they get evaluated by the schema. If the schema returns or sets a new value for the field, that value will continue in the validation chain and be set as the new field value (if not null by the end).

```typescript
schema: {
    #[schema((value: str): str => {
        if (value == null) return "default"; // new field with a default value
        return value;
    })]
    field: ""
}

target: {
    // nothing here
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);

    self.target.field = "dude";
    assert(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":"default"}
{"field":"dude"}
```

## Together (pipeline)

Validations and transformations are helpful, but much more so when combined.

When an array/vec is passed into the #\[schema(...)] attribute, functions are evaluated in order, creating a pipeline for validation and transformation.

In this example, we are placing a form of access control on a field, allowing it to show up in the target if the target object has a valid token. We're then removing the token from the target object since it is not defined in the schema.

```typescript
headers: {
    token: "abcdefghi"

    fn hasAccess(token: str): bool {
        return token == self.token;
    }
}

schema: {
    // return true if this target object has access (validation).
    fn hasAccess(target: obj): bool {
        return super.headers.hasAccess(target.token);
    }

    #[schema([ // evaluated in order (pipeline)
        self.hasAccess,                        // access control (validation)
        (value: str): str => value.toUpper(),  // transformation (if valid)
    ])]
    field: ""
}

target: {
    token: "abcdefghi"
    field: "something cool"
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target, true, true));
    pln(self.target);

    self.target.token = "abcd"; // wrong token
    assertNot(self.schema.schemafy(self.target, true, true));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":"SOMETHING COOL"}
{}
```
