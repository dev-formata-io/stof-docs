---
description: Objects, familiar but slightly different.
---

# Objects

Declarative data formats and languages are represented as DAGs (Directed Acyclic Graphs). Stof is no different, and just like JSON, uses the term "object" to denote DAG relationships between groupings of data.

```json5
// "root" object

// "child" field & object as a child of "root"
child: {
    // "root.child" object
    // contain fields and functions
    
    grandchild: {
        // "root.child.grandchild" object
        // contain fields and functions
    }
}
```

Unlike other data formats, Stof operates on itself from within itself. Therefore, we have a familiar "self" concept, referencing the object we are currently operating within, following typical lexical scoping rules. Stof also has a "super" concept for referencing an object's parent if it exists.

```json5
// referenced via "self.field" from the "root" object
// referenced via "super.field" from the "root.child" object
// can be referenced from any object via "root.field"
field: 'root field'

child: {
    // referenced via "self.child.field" from the "root" object
    // referenced via "self.field" from this "root.child" object
    // can be referenced from any object via "root.child.field"
    field: 'child field'
}
```

## Additional Roots

Also unlike other data formats, Stof can have multiple root objects. Additional roots in Stof are defined with the "root" keyword where a type would go otherwise.

```json5
// field declared in the "root" object
field: 'I am in the normal "root"'

root OtherRoot: {
    // field declared in another root object named "OtherRoot"
    // referenced via "OtherRoot.field" from anywhere
    // referenced via "self.field" from this object
    field: 'I am in the "OtherRoot" root object'
}
```

## Paths

Everything in Stof is referenced via paths: fields, objects, and functions. Paths are dot-separated, with each token of the path referencing an object name until the last token, which can be an object, field, or function.

Stof's path resolution algorithm looks at children first, then the parent, then itself, and then any fields pointing to other objects. If an object does not have a parent (root), then it will look for other roots with that name. The keyword "super" matches with any parent name, and the keyword "self" matches with any object name when matching with itself.

The result is being able to reference data and objects in specific locations from anywhere in the document at all times.

### Children

```rust
fn example() {
    let x = self.child.grandchild.field; // 42    
}

child: {
    grandchild: {
        field: 42
    }
}
```

### Parents

```rust
field: 42

child: {
    grandchild: {
        fn example() {
            let x = super.super.field; // 42
        }
    }
}
```

### Self

```rust
field: 42

fn example() {
    let x = self.self.self.self.self.field; // 42
}
```

### Other Roots

Root objects look at other root object names in place of a parent.

```rust
root Other {
    field: 42
}
child: {
    grandchild: {
        fn example() {
            let absolute = Other.field;
            let relative = super.super.Other.field;
        }
    }
}
```

### Absolute Paths

Instead of using "self" or "super", you can always use absolute paths from a root node to point anywhere in the document. The default root object is named "root".

```rust
child: {
    grandchild: {
        field: 42
    }
}
sibling: {
    grandchild: {
        fn example() {
            let relative = super.super.child.grandchild.field; // 42
            let absolute = root.child.grandchild.field;        // 42
        }
    }
}
```

