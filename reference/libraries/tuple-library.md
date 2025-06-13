---
description: Stof's standard tuple library ("Tuple").
icon: brackets-round
---

# Tuple Library

{% hint style="info" %}
Tuples can be cast to vectors for more available operations.
{% endhint %}

## Common Value Functions

### <mark style="color:purple;">Tuple</mark><mark style="color:orange;">.toString</mark>(tup: <mark style="color:green;">(.., ..)</mark>): <mark style="color:green;">str</mark>

Returns this tuple converted to a string.

```typescript
#[test('(["34", "12"])')]
fn test(): str {
    return (34, 12).toString();
}
```

### <mark style="color:purple;">Tuple</mark><mark style="color:orange;">.or</mark>(tup: <mark style="color:green;">(.., ..)</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let tup: (int, int) = self.dne.or((1, 2));
    assertEq(tup, (1, 2));
}
```

## Tuple Functions

### <mark style="color:purple;">Tuple</mark><mark style="color:orange;">.len</mark>(tup: <mark style="color:green;">(.., ..)</mark>): <mark style="color:green;">int</mark>

Get the number of values contained within this tuple.

```rust
#[test]
fn test() {
    let tup = (12, "hello");
    assertEq(tup.len(), 2);
    assertEq(Tuple.len(tup), 2);
}
```

### <mark style="color:purple;">Tuple</mark><mark style="color:orange;">.at</mark>(tup: <mark style="color:green;">(.., ..)</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">unknown</mark>

Get the value in this tuple at a specific index.

```rust
#[test]
fn test() {
    let tup = (12, 23, 2.2, true, "hi");
    
    assertEq(tup.at(0), 12);
    assertEq(tup[2], 2.2);
    
    for (val in tup) pln(val); // 12, 23, 2.2, true, hi
}
```
