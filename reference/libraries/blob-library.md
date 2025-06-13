---
description: Stof's standard blob library ("Blob").
icon: binary
---

# Blob Library

## Common Value Functions

### <mark style="color:purple;">Blob</mark><mark style="color:orange;">.toString</mark>(blob: <mark style="color:green;">blob</mark>): <mark style="color:green;">str</mark>

Returns this blob converted to a string.

### <mark style="color:purple;">Blob</mark><mark style="color:orange;">.or</mark>(blob: <mark style="color:green;">blob</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let blb = self.dne.or("hello" as blob);
    assertEq(blb.len(), 5);
}
```

## Blob Functions

### <mark style="color:purple;">Blob</mark><mark style="color:orange;">.len</mark>(blob: <mark style="color:green;">blob</mark>): <mark style="color:green;">int</mark>

Get the length of this blob (number of bytes (u8 values)).

```rust
#[test]
fn test() {
    let blb = "dude" as blob;
    assertEq(blb.len(), 4);
    assertEq(blb.size(), 4); // "size" is an alias in the Blob lib for "len"
}
```

### <mark style="color:purple;">Blob</mark><mark style="color:orange;">.at</mark>(blob: <mark style="color:green;">blob</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">int</mark>

Get the byte in this blob at a specific index.

```rust
#[test]
fn at() {
    let blb = "hello" as blob;
    let e = blb[1];
    let o = blb.at(4);
    assertEq(([e, o] as blob) as str, "eo");
}
```
