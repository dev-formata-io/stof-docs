---
description: Stof's standard map library ("Map").
icon: map
---

# Map Library

Maps in Stof are ordered and can contain a mixture of types. Within each type, values are ordered as you would expect.

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

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.toString</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">str</mark>

Returns a map converted to a string. This performs the same as "std.pln(set)" and "std.dbg(set)".

```typescript
#[test('{String("a"): Number(I64(1)), String("b"): Number(I64(2))}')]
fn test(): str {
    return map(("a", 1), ("b", 2)).toString();
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.or</mark>(map: <mark style="color:green;">map</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let map = self.dne.or(map((1, 2), (2, 3)));
    assertEq(map, map((1, 2), (2, 3)));
}
```

## Map Functions

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.append</mark>(map: <mark style="color:green;">map</mark>, other: <mark style="color:green;">map</mark>): <mark style="color:green;">void</mark>

Move all elements from another map into this map, leaving the other map empty.

```rust
#[test]
fn append() {
    let a = box(map([("a", "A")]));
    let b = map([("b", "B")]);
    b.append(a); // unboxed a will copy and a.len() will be 1 still, boxed passes by ref
    assertEq(a.len(), 0);
    assertEq(b.len(), 2);
    assertEq(b.first(), ("a", "A"));
    assertEq(b.last(), ("b", "B"));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.clear</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">void</mark>

Clear this map, removing all elements.

```rust
#[test]
fn clear() {
    let a = map([("a", "A"), (10, 100)]);
    a.clear();
    assertEq(a.len(), 0);
    assert(a.empty());
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.contains</mark>(map: <mark style="color:green;">map</mark>, key: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Does this map contain this key?

```rust
#[test]
fn contains() {
    let a = map(("a", "A"), (42, "meaning of life"));
    assert(a.contains("a"));
    assert(a.contains(42));
    assertNot(a.contains("b"));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.first</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">(unknown, unknown)</mark>

Returns the first key-value pair in this ordered map.

```rust
#[test]
fn first() {
    let a = map([(10, 10), (9, 9), (8, 8), (7, 7)]);
    assertEq(a.first(), (7, 7));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.last</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">(unknown, unknown)</mark>

Returns the last key-value pair in this ordered map.

```rust
#[test]
fn last() {
    let a = map([(10, 10), (9, 9), (8, 8), (7, 7)]);
    assertEq(a.last(), (10, 10));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.get</mark>(map: <mark style="color:green;">map</mark>, key: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark>

Get a value from this map, or null if the map does not contain this key.

```rust
#[test]
fn get() {
    let a = map(
        (10, 22),
        (42, "meaning of life"),
        ("hello", "dude"),
        (("a", "b"), ("A", "B")));
        
    assertEq(a.get(10), 22);
    assertEq(a.get(42), "meaning of life");
    assertEq(a.get("hello"), "dude");
    assertEq(a.get("dne"), null);

    assertEq(a.get(("a", "b")), ("A", "B"));
    assertEq(a.get(("A", "b")), null);
    assertEq(a.get(("a", "a")), null);
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.insert</mark>(map: <mark style="color:green;">map</mark>, key: <mark style="color:green;">unknown</mark>, value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark>

Insert a key-value pair into this map, returning the old value if the map already contains this key, null otherwise.

```rust
#[test]
fn insert() {
    let a = box(map());
    
    a.insert("a", "A");
    a.insert(true, "hello");
    a.insert(1, 1);
    a.insert(44.2, 54.1);
    
    assertEq(a.len(), 4);
    assertEq(a.get("a"), "A");
    assertEq(a.get("b"), null);
    assertEq(a.get(true), "hello");
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.empty</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">bool</mark>

Is this map empty?

```rust
#[test]
fn empty() {
    let a = map();
    assert(a.empty());
    assertNot(a.any());

    a.insert(0, "a");
    assertNot(a.empty());
    assert(a.any());
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.any</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">bool</mark>

Does this map contain any items?

```rust
#[test]
fn any() {
    let a = map();
    assert(a.empty());
    assertNot(a.any());

    a.insert(0, "a");
    assertNot(a.empty());
    assert(a.any());
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.keys</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">vec</mark>

Returns a vector containing this map's keys (sorted).

```rust
#[test]
fn keys_values() {
    let a = map([(3, 6), (1, 4), (2, 5)]);
    assertEq(a.keys(), [1, 2, 3]);
    assertEq(a.values(), [4, 5, 6]);
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.values</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">vec</mark>

Returns a vector containing this map's values (sorted according to keys).

```rust
#[test]
fn keys_values() {
    let a = map([(3, 6), (1, 4), (2, 5)]);
    assertEq(a.keys(), [1, 2, 3]);
    assertEq(a.values(), [4, 5, 6]);
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.len</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">int</mark>

Returns the number of items contained within this map.

```rust
#[test]
fn len() {
    let a = map();
    assertEq(a.len(), 0);
    a.insert("a", "A");
    assertEq(a.len(), 1);
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.at</mark>(map: <mark style="color:green;">map</mark>, index: <mark style="color:green;">int</mark> | <mark style="color:green;">unknown</mark>): <mark style="color:green;">(unknown, unknown)</mark>

Return an item within this map at a specific index. If the index is numerical, it will be interpreted as a position within the ordered map. If the index is any other value, the behavior mimics that of the "get" function but returns a key-value pair if the item exists (null otherwise).

```rust
#[test]
fn at() {
    // recommended that you use 'get', but 'at' works for non-numerical keys also
    let a = map((3, 6), (1, 4), (2, 5));
    
    assertEq(a[0], (1, 4));
    assertEq(a.at(1), (2, 5));
    assertEq(a[2], (3, 6));

    let count = 0;
    for (item in a) {
        count += 1;
        if (first) assertEq(item, (1, 4));
        else if (last) assertEq(item, (3, 6));
        else assertEq(item, (2, 5));
    }
    assertEq(count, 3);
}

#[test]
fn at_non_index() {
    let a = map([(3, 6), (1, 4), ("hi", 5)]);
    assertEq(a["hi"], ("hi", 5));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.popFirst</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">(unknown, unknown)</mark>

Remove the first item of this map and return it.

```rust
#[test]
fn pop_first() {
    let a = map([(3, 6), (1, 4), (2, 5)]);
    assertEq(a.popFirst(), (1, 4));
    assertEq(a.len(), 2);
    assertEq(a.first(), (2, 5));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.popLast</mark>(map: <mark style="color:green;">map</mark>): <mark style="color:green;">(unknown, unknown)</mark>

Remove the last item of this map and return it.

```rust
#[test]
fn pop_last() {
    let a = map([(3, 6), (1, 4), (2, 5)]);
    assertEq(a.popLast(), (3, 6));
    assertEq(a.len(), 2);
    assertEq(a.last(), (2, 5));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.remove</mark>(map: <mark style="color:green;">map</mark>, key: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark>

Remove the item in this map with the given key and return it's value, or null if the item does not exist.

```rust
#[test]
fn remove() {
    let a = map([(3, 6), (1, 4), (2, 5), ("hi", 100)]);
    
    let val: int = a.remove("hi");
    assertEq(val, 100);
    
    val = a.remove("hi");
    assertNull(val);

    val = a.remove(3);
    assertEq(val, 6);

    assertEq(a.len(), 2);
    assertEq(a.first(), (1, 4));
    assertEq(a.last(), (2, 5));
}
```

### <mark style="color:purple;">Map</mark><mark style="color:orange;">.retain</mark>(map: <mark style="color:green;">map</mark>, predicate: <mark style="color:green;">fn</mark>): <mark style="color:green;">void</mark>

Retain only the elements in this map specified by the predicate. The predicate function takes two parameters (key and value) and returns a boolean, indicating whether to keep or remove the key-value pair from the map.

```rust
#[test]
fn retain() {
    let a = map((3, 6), (1, 4), (2, 5), ("hi", 100));
    a.retain((key: unknown, value: unknown): bool => (typeof key) == 'str');
    assertEq(a.len(), 1);
    assertEq(a.first(), a.last());
    assertEq(a.first(), ("hi", 100));
}
```

## Map Operations

### Union

```typescript
#[test]
fn union() {
    let a = map([('a', 'A')]);
    let b = map([('b', 'B')]);
    let c = a + b; // union operation
    assertEq(c.toString(), '{String("a"): String("A"), String("b"): String("B")}');
}
```

### Difference

```typescript
#[test]
fn diff_map() {
    let a = map([('a', 'A'), ('b', 'B')]);
    let b = map([('b', 'B')]);
    let c = a - b;
    assertEq(c.toString(), '{String("a"): String("A")}');
}

#[test]
fn diff_vec() {
    let a = map([('a', 'A'), ('b', 'B')]);
    let b = ['b'];
    let c = a - b;
    assertEq(c.toString(), '{String("a"): String("A")}');
}
```

### Intersection

```typescript
#[test]
fn intersect_map() {
    let a = map([('a', 'A'), ('b', 'B'), ('c', 'C')]);
    let b = map([('c', 'C'), ('d', 'D'), ('e', 'E')]);
    let c = a * b;
    assertEq(c.toString(), '{String("c"): String("C")}');
}

#[test]
fn intersect_vec() {
    let a = map([('a', 'A'), ('b', 'B'), ('c', 'C')]);
    let b = vec('c', 'd', 'e');
    let c = a * b;
    assertEq(c.toString(), '{String("c"): String("C")}');
}
```

### Symmetric Difference

```typescript
#[test]
fn symmetric_difference() {
    let a = map([('a', 'A'), ('b', 'B'), ('c', 'C')]);
    let b = map([('c', 'C'), ('d', 'D'), ('e', 'E')]);
    let c = a % b;
    assertEq(c.toString(), '{String("a"): String("A"), String("b"): String("B"), String("d"): String("D"), String("e"): String("E")}');
}
```
