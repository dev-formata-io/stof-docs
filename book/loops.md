---
description: Looping.
---

# Loops

Just like other programming languages, Stof has loops: while, for, & loop.

## While

```rust
#[main]
fn main() {
    let count = 100;
    while (count > 0) {
        count -= 1;
    }
}
```

## Loop

A "loop" is a "while (true)" loop.

```rust
#[main]
fn main() {
    let count = 100;
    loop {
        count -= 1;
        if (count <= 0) break;
    }
}
```

## For

```rust
#[main]
fn main() {
    for (let i = 0; i < 100; i += 1) {
        // do something with i
    }
}
```

```rust
#[main]
fn main() {
    // the 100 here can be replaced with any value that has len() and at(index) funcs
    // standard library defines these for lists, sets, maps, objects, etc.
    for (const i in 100) {
        // do something with i
        pln(index); // for in loop defines an index var for you
        pln(first); // true for the first iteration
        pln(last);  // true for the last iteration
    }
}
```

For loops can take values by reference as well, with the "&" operator.

```rust
#[main]
fn main() {
    const list = [1, 2, 3];
    for (let val in &list) val += 2;
    
    assert_eq(list, [3, 4, 5]);
}
```

## Tagging

All loops can be tagged for greater control and readability (while, loop, & for). Once a loop is tagged, both "continue ^tag" and "break ^tag" will control that loop, regardless of nested loop structure.

```rust
iterator: {
    len: ():int=>10
    at: (index:int):int =>index
}

#[main]
fn main() {
    let res = false;
    ^tag for (const val in self.iterator) {
        for (let i = 10; i > 0; i -= 1) {
            if (i < 4) {
                res = true;
                break ^tag; // breaks the outer loop with the tag "tag"
            }
        }
        res = false;
    }
    assert(res);
}
```
