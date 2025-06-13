---
description: Apply nested schemas to nested objects.
---

# Nested Schemas

Commonly, objects contain other objects that also need to be schematized. This could be an object field, objects contained within arrays, etc. Schemafy has you covered no matter the case.

## Object Field

For objects, [schemafy](../../reference/libraries/object-library.md) has a really handy shortcut to apply nested schemas. When used, options for removing invalid fields and fields that are not defined on the schema propagate downwards (the additional schemafy parameters).

One always has access to everything in the document though, so the long way of calling schemafy manually would work as well. This might be helpful if you don't want the same behavior for removing invalid fields for all nested schemas, or if you want to apply a schema defined somewhere else.

```typescript
schema: {
    // shorthand for: #[schema((value: obj): bool => self.field.schemafy(value))]
    #[schema]
    field: {
        #[schema((value: str): bool => value.len() > 1)]
        name: ""
    }
}

target: {
    field: {
        name: "Bob"
    }
}

#[main]
fn main() {
    assert(self.schema.schemafy(self.target));
    pln(self.target);

    self.target.field.name = "A";
    assertNot(self.schema.schemafy(self.target));
    pln(self.target);
}
```

```
> stof run example.stof
{"field":{"name":"Bob"}}
{}
```

## Multiple Schemas

```typescript
another: {
    #[schema]
    names: {
        #[schema([
            (value: unknown): bool => value != null,
            (value: vec): bool => value.len() > 0,
            (value: vec): bool => { for (v in value) if (!isString(v)) return false; return true; }
        ])]
        given: []
    }
}

person: {
    // looks for a names field to apply
    // additional schmeas can also be provided in arrays/nested pipelines (applied in order)
    #[schema(super.another)]
    names: {}
}

#[main]
fn main() {
    let valid = new {
        names: new {
            given: ["Bob", "Miller"]
            dontcare: "hello"
        }
    };
    assert(self.person.schemafy(valid, true, true));
    pln(valid);

    let invalid = new {
        names: new {
            given: ["Bob", 32]
        }
    };
    assertNot(self.person.schemafy(invalid));
    pln(invalid);
}
```

```
> stof run example.stof
{"names":{"given":["Bob","Miller"]}}
{}
```

## Collections

Collections are validated/transformed just like any other field. It is up to you to iterate over values to validate/transform collections. This can be done with additional schemafy calls, or however you'd like.

```typescript
schema: {
    #[schema((value: Box<vec>) => value.retain((item: int): bool => item % 2 == 0))]
    collection: []
}

#[main]
fn main() {
    let target = new {
        collection: 0..20
    };
    assert(self.schema.schemafy(target));
    pln(target);
}
```

```
> stof run example.stof
{"collection":[0,2,4,6,8,10,12,14,16,18]}
```
