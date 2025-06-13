---
description: Stof's standard string library ("String").
icon: quotes
---

# String Library

## Common Value Functions

### <mark style="color:purple;">String</mark><mark style="color:orange;">.toString</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Returns this string as it would be printed to the console.

### <mark style="color:purple;">String</mark><mark style="color:orange;">.or</mark>(val: <mark style="color:green;">str</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
name: "Bob Smith"

#[test("Bob Smith")]
fn test(): str {
    return self.name.or("unknown");
}
```

## String Functions

### <mark style="color:purple;">String</mark><mark style="color:orange;">.len</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark>

Return the length of this string.

```rust
#[test(5)]
fn test(): int {
    return String.len("hello"); // "hello".len() works too of course
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.at</mark>(val: <mark style="color:green;">str</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">str</mark>

Get a character from this string at a specific index. The functions "at" and "len" together enable iteration with for loops as well.

```rust
#[test]
fn test() {
    let val = "hello";
    assertEq(val.at(1), "e");
    assertEq(val[3], "l");
    
    for (ch in val) pln(ch); // "hello" one char at a time
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.first</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Return the first character in the string, or null if the string is empty.

```rust
#[test("h")]
fn test(): str {
    return "hello".first();
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.last</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Return the last character in the string, or null if the string is empty.

```rust
#[test("o")]
fn test(): str {
    return "hello".last();
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.startsWith</mark>(val: <mark style="color:green;">str</mark>, substr: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Return true if the string starts with "substr".

```rust
#[test]
fn test() {
    let string = "hi there";
    assert(string.startsWith("hi"));
    assertNot(string.startsWith("yo"));
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.endsWith</mark>(val: <mark style="color:green;">str</mark>, substr: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Return true if the string ends with "substr".

```rust
#[test]
fn test() {
    let string = "hi there";
    assert(string.endsWith("ere"));
    assertNot(string.endsWith("yo"));
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.push</mark>(val: <mark style="color:green;">str</mark>, ...additional: <mark style="color:green;">unknown</mark>): <mark style="color:green;">void</mark>

Push additional arguments (turned into strings) to the end of "val".

```rust
#[test]
fn test() {
    let val = "hello";
    val.push(", ", "world", "!");
    assertEq(val, "hello, world!");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.contains</mark>(val: <mark style="color:green;">str</mark>, substr: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Return true if "val" contains the substring.

```rust
#[test]
fn test() {
    assert("my name is blah".contains("name is"));
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.indexOf</mark>(val: <mark style="color:green;">str</mark>, substr: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark>

Return the index of the first character of "substr" found in "val". If not found, returns -1.

```rust
#[test]
fn test() {
    assertEq("hello dude".indexOf("dude"), 6);
    assertEq("blah".indexOf("dne"), -1);
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.replace</mark>(val: <mark style="color:green;">str</mark>, from: <mark style="color:green;">str</mark>, to: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Replace all instances of a substring in this string, returning a new string.

```rust
#[test]
fn test() {
    let val = "this.is.a.good.day";
    assertEq(val.replace(".", " "), "this is a good day");
    assertEq(val, "this.is.a.good.day");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.split</mark>(val: <mark style="color:green;">str</mark>, substr: <mark style="color:green;">str</mark>): <mark style="color:green;">vec</mark>

Split this string into a vector of strings, separated at every occurrence of "substr".

```rust
#[test]
fn test() {
    let val = "this.is.a.good.day";
    assertEq(val.split("."), ["this", "is", "a", "good", "day"]);
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.toUpper</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Transforms this string to all uppercase letters, returning a new string.

```rust
#[test]
fn test() {
    assertEq("hello".toUpper(), "HELLO");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.toLower</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Transforms this string to all lowercase letters, returning a new string.

```rust
#[test]
fn test() {
    assertEq("HELLO".toLower(), "hello");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.trim</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Will trim the whitespace off of the start and end of this string (spaces, newlines, tabs), returning a new string.

```rust
#[test]
fn test() {
    assertEq("  trim  ".trim(), "trim");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.trimStart</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Will trim the whitespace off of the start of this string (spaces, newlines, tabs), returning a new string.

```rust
#[test]
fn test() {
    assertEq("  trim  ".trimStart(), "trim  ");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.trimEnd</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Will trim the whitespace off of the end of this string (spaces, newlines, tabs), returning a new string.

```rust
#[test]
fn test() {
    assertEq("  trim  ".trimEnd(), "  trim");
}
```

### <mark style="color:purple;">String</mark><mark style="color:orange;">.substring</mark>(val: <mark style="color:green;">str</mark>, start: <mark style="color:green;">int</mark>, end?: <mark style="color:green;">int</mark>): <mark style="color:green;">str</mark>

Returns a substring of "val" from a starting index up to (but not including) an optional ending index. If the ending index is omitted, the resulting substring will be from a starting index to the end of "val" (default end is the length of "val").

```rust
#[test]
fn test() {
    let val = "hello, world";
    assertEq(val.substring(2, 5), "llo");
    assertEq(val.substring(7), "world");
}
```
