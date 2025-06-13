---
description: An overview of Stof fields.
---

# Fields

Fields are the primary way to define data with Stof (but not the only way).

```json5
field: 42; // A field named "field" with the value 42 (int)
```

## Paths

Just like objects and functions, fields are organized in objects and are referenced via paths. See the [objects.md](objects.md "mention") page for more details on paths and how they work.

```json5
// referenced via "root.field" from anywhere in the document
// referenced from the "root" object as "self.field"
field: 'hello'

child: {
    // referenced via "root.child.field" from anywhere in the document
    // referenced via "self.child.field" from the "root" object
    // referenced via "self.field" from this "root.child" object
    field: 42
}
```

## Attributes

Just like functions, fields can have attributes. Currently, however, there are not many field attributes that Stof pays attention to. They mostly benefit anyone using Stof to interpret fields in different ways.

### Private Attribute

Fields can have a private attribute, making them only visible and operable in the object(s) in which they are attached.

```rust
child: {
    #[private]
    field: 42

    #[test]
    fn can_access() {
        assertEq(self.field, 42);

        self.field = 'hello';
        assertEq(self.field, 'hello');
    }

    grandchild: {
        #[test]
        fn cannot_access() {
            assertEq(super.field, null);
        }
    }
}

#[test]
fn cannot_access() {
    assertEq(self.child.field, null);
}
```

### Read Only Attribute

Fields with the "readonly" attribute cannot be modified by functions, only read.

```rust
#[readonly]
field: 42

#[test]
fn cannot_modify() {
    assertEq(self.field, 42);

    self.field = 'hello'; // Field cannot be written to
    assertEq(self.field, 42);
}
```
