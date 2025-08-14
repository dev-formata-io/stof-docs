---
description: Field data.
---

# Fields

Fields are the most obvious way to define and store data in a Stof document. Coming from other data formats like JSON or YAML, fields will feel very intuitive and familiar.

Navigation with paths can follow field names, also, so programmatically, it feels quite intuitive to work with.

```rust
Object: {
    strings: "my string data"
    numbers: 10ft + 3.55m
    
    Sub: {
        booleans: false
        lists: [
            {
                field: 42
                
                fn value() -> int {
                    self.field + 10
                }
            },
            +1_000,
            "Json arrays are Stof lists",
        ]
    }
}

#[main]
fn main() {
    const list_field = self.Object.Sub.lists;
    const value = list_field.front().value();
    assert_eq(value, 52);
}
```

## Types

Fields that are defined with a type will keep that type, casting values to that given type when assigned. Otherwise, fields can have any value given to them!

```rust
str field: "temp"

#[main]
fn main() {
    self.field = 42;
    assert_eq(typeof self.field, "str");
    assert_eq(self.field, "42");
}
```

## Const

Fields can also be defined as constant, throwing an error if an assignment occurs.

```rust
const str field: "perm"

#[main]
fn main() {
    let caught; // null initialization
    
    try self.field = 42;
    catch caught = true;
    
    assert(caught);
}
```

## Private

Fields can also be private to the object that defines them.

```rust
Inner: {
    #[private]
    private_field: 'secret'

    #[test]
    fn can_see() {
        assert_eq(self.private_field, 'secret');
    }

    InnerInner: {
        #[test]
        fn cannot_see() {
            assert_not(super.private_field);
        }
    }
}

#[test]
fn cannot_see() {
    assert_not(self.Inner.private_field);
}
```
