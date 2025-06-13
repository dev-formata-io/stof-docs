---
description: Stof's standard set library ("Set").
icon: snowflake
---

# Set Library

Sets in Stof are ordered and can contain a mixture of types. Within each type, values are ordered as you would expect.

Mixed-Value Ordering:

1. Null values
2. Booleans
3. Numbers
4. Strings
5. Objects - sorted by ID
6. Functions - sorted by ID
7. Arrays/Vectors
8. Tuples
9. Blobs
10. Sets
11. Maps

## Common Value Functions

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.toString</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">str</mark>

Returns a set converted to a string. This performs the same as "std.pln(set)" and "std.dbg(set)".

```rust
#[test("{Number(I64(1)), Number(I64(2)), Number(I64(3))}")]
fn test(): str {
    return set(1, 2, 3).toString();
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.or</mark>(set: <mark style="color:green;">set</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let set = self.dne.or(set(1, 2));
    assertEq(set, set(1, 2));
}
```

## Set Functions

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.append</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">void</mark>

Move all values in "other" to this set, leaving "other" empty.

```rust
#[test]
fn append() {
    let a: set = set([1, 2]);
    let b = set([2, 3]);
    a.append(b);
    assertEq(a, set(1, 2, 3));
    assertEq(b, set(2, 3));
}

#[test]
fn append_boxed() {
    let a: Box<set> = set([1, 2]);
    let b = box(set([2, 3]));
    a.append(b);
    assertEq(a, set(1, 2, 3));
    assertEq(b, set());
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.clear</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">void</mark>

Remove all values in this set.

```rust
#[test]
fn clear() {
    let a = set(1, 2, 3);
    assertEq(a.len(), 3);
    a.clear();
    assertEq(a.len(), 0);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.contains</mark>(set: <mark style="color:green;">set</mark>, val: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Does this set contain the value?

```rust
#[test]
fn contains() {
    let a = set(('a', 10), true, 'hello', 5);
    assert(a.contains(('a', 10)));
    assert(a.contains(true));
    assertNot(a.contains(false));
    assert(a.contains('hello'));
    assertNot(a.contains('dne'));
    assert(a.contains(5));
    assertNot(a.contains(5.1));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.first</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">unknown</mark>

Returns the first value in this set, or null if the set is empty.

```rust
#[test]
fn first() {
    let a = set(10, 232, 23, 1, 83, -2, 289);
    assertEq(a.first(), -2);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.last</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">unknown</mark>

Returns the last value in this set, or null if the set is empty.

```rust
#[test]
fn last() {
    let a = set(10, 232, 23, 1, 83, -2, 289);
    assertEq(a.last(), 289);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.insert</mark>(set: <mark style="color:green;">set</mark>, val: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Insert a value into this set, returning true if this value is newly inserted.

```rust
#[test]
fn insert() {
    let a = set();
    assert(a.insert(10));
    assertNot(a.insert(10));
    assertEq(a, set(10));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.take</mark>(set: <mark style="color:green;">set</mark>, val: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark>

Take a value from this set if the set contains it (removing the value from the set).

```rust
#[test]
fn take() {
    let a = set(10);
    assertEq(a.take(2), null);
    assertEq(a.take(10), 10);
    assertEq(a.len(), 0);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.split</mark>(set: <mark style="color:green;">set</mark>, val: <mark style="color:green;">unknown</mark>): <mark style="color:green;">set</mark>

Split this set into another set at the given value.

```rust
#[test]
fn split() {
    let a = set(0, 1, 2, 3, 4, 5, 6);
    let b = a.split(3);
    assertEq(a, set(0, 1, 2));
    assertEq(b, set(3, 4, 5, 6));

    let c = b.split(0);
    assertEq(b, set());
    assertEq(c, set(3, 4, 5, 6));

    let d = c.split(7);
    assertEq(c, set(3, 4, 5, 6));
    assertEq(d, set());
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.empty</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">bool</mark>

Is this set empty?

```rust
#[test]
fn empty_any() {
    let a = set();
    assert(a.empty());
    assertNot(a.any());

    a.insert(10);
    assert(a.any());
    assertNot(a.empty());
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.any</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">bool</mark>

Does this set contain any values?

```rust
#[test]
fn empty_any() {
    let a = set();
    assert(a.empty());
    assertNot(a.any());

    a.insert(10);
    assert(a.any());
    assertNot(a.empty());
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.len</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">int</mark>

Returns the length of this set (the number of elements it contains).

```rust
#[test]
fn len() {
    let a = set();
    assertEq(a.len(), 0);
    a.insert(10);
    assertEq(a.len(), 1);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.at</mark>(set: <mark style="color:green;">set</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">unknown</mark>

Get a value in this set at the given index.

```rust
#[test]
fn at() {
    let a = set(0, 1, 2, 3, 4, 5, 6, 7);
    assertEq(a.at(1), 1);
    assertEq(a[5], 5);

    let count = 0;
    for (val in a) {
        if (first) assertEq(val, 0);
        if (last) assertEq(val, 7);
        count += 1;
    }
    assertEq(count, 8);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.popFirst</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">unknown</mark>

Remove the first value in the set and return it.

```rust
#[test]
fn pop_first() {
    let a = set(0, 1, 2, 3, 4);
    assertEq(a.popFirst(), 0);
    assertEq(a.popFirst(), 1);
    assertEq(a.len(), 3);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.popLast</mark>(set: <mark style="color:green;">set</mark>): <mark style="color:green;">unknown</mark>

Remove the last value in the set and return it.

```rust
#[test]
fn pop_last() {
    let a = set(0, 1, 2, 3, 4);
    assertEq(a.popLast(), 4);
    assertEq(a.popLast(), 3);
    assertEq(a.len(), 3);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.remove</mark>(set: <mark style="color:green;">set</mark>, val: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Remove a value from this set, returning true if the value existed.

```rust
#[test]
fn remove() {
    let a = set(1, 2, 3, 4);
    assert(a.remove(3));
    assertNot(a.remove(3));
    assertEq(a.len(), 3);
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.retain</mark>(set: <mark style="color:green;">set</mark>, func: <mark style="color:green;">fn</mark>): <mark style="color:green;">void</mark>

Retain only the elements specified by the predicate.

```rust
#[test]
fn retain() {
    let a = 0..10 as set;
    a.retain((val: int): bool => val % 2 == 0);
    assertEq(a, set(0, 2, 4, 6, 8));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.union</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">set</mark>

Union this set with another, returning a new set.

```rust
#[test]
fn union() {
    let a = set(1, 2);
    let b = set(2, 3);

    let c = a.union(b);
    let d = a + b;
    assertEq(c, d);
    assertEq(c, set(1, 2, 3));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.difference</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">set</mark>

Return a new set which is the difference between this set and another.

```rust
#[test]
fn difference() {
    let a = set(1, 2);
    let b = set(2, 3);

    let c = a.difference(b);
    let d = a - b;
    assertEq(c, d);
    assertEq(c, set(1));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.intersection</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">set</mark>

Return a new set which is the intersection between this set and another.

```rust
#[test]
fn intersection() {
    let a = set(1, 2);
    let b = set(2, 3);

    let c = a.intersection(b);
    let d = a * b;
    assertEq(c, d);
    assertEq(c, set(2));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.symmetricDifference</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">set</mark>

Return a new set which is the symmetric difference between this set and another.

```rust
#[test]
fn symmetric_diff() {
    let a = set(1, 2);
    let b = set(2, 3);

    let c = a.symmetricDifference(b);
    let d = a % b;
    assertEq(c, d);
    assertEq(c, set(1, 3));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.disjoint</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">bool</mark>

Returns true if this set has no elements in common with another.

```rust
#[test]
fn disjoint() {
    let a = set(1, 2);
    let b = set(2, 3);
    let c = set(3, 4);
    assert(a.disjoint(c));
    assert(c.disjoint(a));
    assertNot(a.disjoint(b));
    assertNot(b.disjoint(a));
    assertNot(b.disjoint(c));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.subset</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">bool</mark>

Returns true if this set is a subset of another.

```rust
#[test]
fn subset() {
    let a = set(1, 2);
    let b = set(1, 2, 3);
    assert(a.subset(b));
    assertNot(b.subset(a));
}
```

### <mark style="color:purple;">Set</mark><mark style="color:orange;">.superset</mark>(set: <mark style="color:green;">set</mark>, other: <mark style="color:green;">set</mark>): <mark style="color:green;">bool</mark>

Returns true if this set is a superset of another.

```rust
#[test]
fn superset() {
    let a = set(1, 2);
    let b = set(1, 2, 3);
    assertNot(a.superset(b));
    assert(b.superset(a));
}
```
