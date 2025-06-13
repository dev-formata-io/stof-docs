---
description: Stof's standard array/vector library ("Array").
icon: brackets-square
---

# Array/Vector Library

In Stof, arrays and vectors are the same thing. We support both nomenclatures because it is common for "array" to be used in data formats, however, an array in most programming contexts is a fixed-capacity compound type that cannot grow in size. In Stof, arrays are implemented as vectors, and the "vec" type makes more sense. This separation also helps distinguish the arrays declared in a document from the "runtime" vectors used to manipulate data.

{% hint style="info" %}
Anywhere a <mark style="color:green;">vec</mark> type is supported, a <mark style="color:green;">Box\<vec></mark> type is also supported. If a boxed value is passed in and manipulated, it will be manipulated anywhere else it is also referenced. See [primitive-types.md](../../common-concepts/primitive-types.md "mention") for more information on the Box type.
{% endhint %}

## Common Value Functions

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.toString</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">str</mark>

```rust
#[test]
fn test() {
    pln([1, 2, 3].toString());       // [1, 2, 3]
    pln(Array.toString([4, 5, 6]));  // [4, 5, 6]
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.or</mark>(array: <mark style="color:green;">vec</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let val = [1, 2, 3];
    let default = [4, 5];
    
    let x = val.or(default, []);
    assertEq(x, [1, 2, 3]);
    
    val = null;
    let y = val.or(default, 42, "hello", []);
    assertEq(y, [4, 5]);
}
```

## Vector Functions

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.append</mark>(array: <mark style="color:green;">vec</mark>, other: <mark style="color:green;">vec</mark>): <mark style="color:green;">void</mark>

Appends another vector into this array, leaving the other empty. If not boxed, "other" will be cloned when this function is called, and the original vector maintains its values.

```rust
#[test]
fn append() {
    let a = [1, 2, 3, 4, 5];
    let b = [6, 7, 8, 9];
    a.append(b);
    assertEq(a, [1, 2, 3, 4, 5, 6, 7, 8, 9]);
    assertEq(b, [6, 7, 8, 9]);
}

#[test]
fn append_boxed() {
    let a = [1, 2, 3, 4, 5];
    let b = box([6, 7, 8, 9]);
    a.append(b);
    assertEq(a, [1, 2, 3, 4, 5, 6, 7, 8, 9]);
    assertEq(b, []);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.push</mark>(array: <mark style="color:green;">vec</mark>, ...): <mark style="color:green;">void</mark>

Pushes all arguments to the end of the array in order, as they are given.

```rust
#[test(["hi"])]
fn push(): vec {
    let array = [];
    array.push("hi");
    return array;
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.pop</mark>(array: <mark style="color:green;">vec</mark>, index?: <mark style="color:green;">int</mark> | <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark> | <mark style="color:green;">null</mark>

Pop a value from this array. If an index is not provided, the last element of the array will be removed and returned (null if the array is empty).

If the index is a number, it will be treated as the position within the array to remove and return. An error will be thrown if this index is greater than or equal to the length of the array (indices start at zero).

If the index is a value other than a number, the first value in the array that equals the index value will be removed and returned. If no value in the array equals the index, null is returned.

{% hint style="info" %}
It is preferred to use the "remove", "removeLast", or "removeAll" functions for removing values by equality rather than by index/position.
{% endhint %}

```rust
#[test(["hi"])]
fn pop(): vec {
    let array = ["hi", "there"];
    assertEq(array.pop(), "there");
    return array;
}

#[test([1, 2, 4, 5, 6])]
fn pop_at(): vec {
    let array = [1, 2, 3, 4, 5, 6];
    assertEq(array.pop(2), 3); // if given a number param, it is treated as an index
    return array;
}

#[test(["a", "c", "d", "e"])]
fn pop_val(): vec {
    let array: vec = ["a", "b", "c", "d", "e"];
    assertEq(array.pop("b"), "b");
    return array;
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.reverse</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">void</mark>

Reverses the array in place.

```rust
#[test]
fn reverse() {
    let array = [1, 2, 3, 4];
    array.reverse();
    assertEq(array, [4, 3, 2, 1]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.reversed</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">vec</mark>

Clones this array in the reverse order and returns it, leaving the original array unmodified.

```rust
#[test]
fn reversed() {
    let array = [1, 2, 3, 4];
    let reversed = array.reversed();

    assertEq(array, [1, 2, 3, 4]);
    assertEq(reversed, [4, 3, 2, 1]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.len</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">int</mark>

The length of this array.

```rust
#[test]
fn len() {
    let array = [1, 2, 3, 4, 5, 6, 7, 8];
    assertEq(array.len(), 8);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.empty</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">bool</mark>

Is this array empty?

```rust
#[test]
fn empty() {
    let array = [];
    assert(array.empty());

    array.push(1);
    assertNot(array.empty());
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.any</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">bool</mark>

Does this array have any values (not empty)?

```rust
#[test]
fn any() {
    let array = [];
    assertNot(array.any());

    array.push(1);
    assert(array.any());
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.at</mark>(array: <mark style="color:green;">vec</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">unknown</mark>

Get a value in this array at a specific index. Will throw an error if the index is out of bounds.

{% hint style="info" %}
Any value/object that implements an "at" function and a "len" function can be used with for-loops in Stof.
{% endhint %}

{% hint style="info" %}
Boxing values inside an array will enable you to manipulate values without setting them.
{% endhint %}

```rust
#[test]
fn at() {
    let array = [1, 2, 3, 4];

    assertEq(array.at(2), 3);
    assertEq(array[3], 4);
}

#[test]
fn nested_at() {
    let array = [[1, 2, 3], [4, 5, 6]];
    assertEq(array[0][1], 2);
    assertEq(array.at(1).at(2), 6);
}

#[test]
fn loops() {
    let vals = [];
    for (i in 0..10) vals.push(i);  // 0..10 is a range array constructor
    assertEq(vals, [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.first</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">unknown</mark> | <mark style="color:green;">null</mark>

Returns the first value in the array (cloned) or null if the array is empty.

```rust
#[test(1)]
fn first(): int {
    let array = [1, 2, 3];
    return array.first();
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.last</mark>(array: <mark style="color:green;">vec</mark>): <mark style="color:green;">unknown</mark> | <mark style="color:green;">null</mark>

Returns the last value in the array (cloned) or null if the array is empty.

```rust
#[test(3)]
fn last(): int {
    let array = [1, 2, 3];
    return array.last();
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.join</mark>(array: <mark style="color:green;">vec</mark>, separator: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Joins the values inside this array into a singular string, separated by a "separator" value. This array is unmodified.

```rust
#[test("hello, world")]
fn join(): str {
    return ["hello", "world"].join(", ");
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.contains</mark>(array: <mark style="color:green;">vec</mark>, value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Does this array contain a value (equality comparison)?

```rust
#[test]
fn contains() {
    let array = ["hello", "world"];
    assert(array.contains("hello"));
    assertNot(array.contains("dne"));
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.find</mark>(array: <mark style="color:green;">vec</mark>, other: <mark style="color:green;">unknown</mark>): <mark style="color:green;">int</mark>

Returns the index of the first value in this array to equal "other". If not found, returns -1.

```rust
#[test]
fn find() {
    let array = ["hello", "world", 5];
    assertEq(array.find(5), 2);
    assertEq(array.find("world"), 1);
    assertEq(array.find("dne"), -1);
    assertEq(array.find(2), -1);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.remove</mark>(array: <mark style="color:green;">vec</mark>, other: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark> | <mark style="color:green;">null</mark>

Removes the first value in the array to equal "other" and returns it. Returns null if not found.

```rust
#[test]
fn remove_first_occurence() {
    let array = ["hello", "world", 5, 4, "world"];
    
    assertEq(array.remove("dne"), null);
    assertEq(array.remove(5), 5);
    assertEq(array.remove("world"), "world");
    assertEq(array, ['hello', 4, 'world']);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.removeLast</mark>(array: <mark style="color:green;">vec</mark>, other: <mark style="color:green;">unknown</mark>): <mark style="color:green;">unknown</mark> | <mark style="color:green;">null</mark>

Removes the last value in the array to equal "other" and returns it. Returns null if not found.

```rust
#[test]
fn remove_last_occurence() {
    let array = ["hello", "world", 4, "world"];
    assertEq(array.removeLast("world"), "world");
    assertEq(array, ["hello", "world", 4]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.removeAll</mark>(array: <mark style="color:green;">vec</mark>, other: <mark style="color:green;">unknown</mark>): <mark style="color:green;">vec</mark>

Removes all values in the array that equal "other", returning a vector of all values removed.

```rust
#[test]
fn remove_all() {
    let array = [1, 1, 1, 1, 2, 1, 1, 1, 1];
    assertEq(array.removeAll(1).len(), 8);
    assertEq(array, [2]);
    assert(array.removeAll(1).empty());
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.insert</mark>(array: <mark style="color:green;">vec</mark>, index: <mark style="color:green;">int</mark>, ...values: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Inserts values into this array at a specific index, shifting existing elements to the right. Must provide at least one value to insert.

Will throw an error if the index is out of bounds or if you do not provide at least one value to insert.

```rust
#[test]
fn insert() {
    let array = [1, 2, 3, 4, 5, 6];
    array.insert(2, "hello");
    assertEq(array, [1, 2, 'hello', 3, 4, 5, 6]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.set</mark>(array: <mark style="color:green;">vec</mark>, index: <mark style="color:green;">int</mark>, ...values: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Inserts values into this array at a specific index, replacing the existing element at that index. Must provide at least one value to insert.

Will throw an error if the index is out of bounds or if you do not provide at least one value to insert.

```rust
#[test]
fn set() {
    let array = [1, 2, 3, 4, 5, 6];
    array.set(2, "hello");
    assertEq(array, [1, 2, "hello", 4, 5, 6]);

    array.set(4, 42);
    assertEq(array, [1, 2, "hello", 4, 42, 6]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.iter</mark>(array: <mark style="color:green;">vec</mark>, func: <mark style="color:green;">fn</mark>): <mark style="color:green;">void</mark>

Iterates over this array, calling "func" for each value. If "func" returns a non-null value, that value is then set in place of the existing element. The function passed in must take a singular value as a parameter, in the type you know you are iterating over (or "unknown" to accept all).

```rust
#[test]
fn iter() {
    let array = 0..15;
    array.iter((val: int): int => {
        if (val % 2 == 0) {
            return val + 1;
        }
        return null; // don't set anything
    });
    assertEq(array, [1, 1, 3, 3, 5, 5, 7, 7, 9, 9, 11, 11, 13, 13, 15]);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.retain</mark>(array: <mark style="color:green;">vec</mark>, func: <mark style="color:green;">fn</mark>): <mark style="color:green;">void</mark>

For each value in this array, call "func" (passing the value in as the only argument) - if "func" returns true, keep the value, otherwise, remove the value from the array.

```rust
#[test]
fn retain_evens() {
    let array = 0..100;
    array.retain((v: int): bool => v % 2 == 0);
    assertEq(array, 0..100|2);
}
```

### <mark style="color:purple;">Array</mark><mark style="color:orange;">.sort</mark>(array: <mark style="color:green;">vec</mark>, func?: <mark style="color:green;">fn</mark>): <mark style="color:green;">void</mark>

Sort this array, optionally providing a function to sort with.

If a function is not provided, Stof will use the "less-than" and "greater-than" built-in functions to compare values.

If providing a function, the function should take two value arguments and return -1 for less than, 1 for greater than, or 0 for equal.

```rust
#[test([0, 1, 2, 2, 4, 5, 5, 7, 8])]
fn sort(): vec {
    let array = [5, 2, 4, 5, 7, 8, 0, 2, 1];
    array.sort();
    return array;
}

#[test([2, 3, 5, 6, 6])]
fn sort_by(): vec {
    let array = [new {k:6}, new {k:3}, new {k:5}, new {k:2}, new {k:6}];
    array.sort((a: obj, b: obj): int => {
        if (a.k < b.k) return -1;
        if (a.k > b.k) return 1;
        return 0;
    });
    let res = [];
    for (object in array) {
        res.push(object.k);
    }
    return res;
}
```
