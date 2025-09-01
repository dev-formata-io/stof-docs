---
description: Null values, initialization, & practical usage.
---

# Null & Initialization

The null value is a controversial one in computer science, sometimes [referenced as a mistake](https://news.ycombinator.com/item?id=18588239).

Putting personal opinion aside, the null concept is prevalent in a lot of languages and data formats. Because Stof's purpose is to hold all of the stuff and glue environments together, it has a null value concept.

The main problem with null is that it subverts a language's type system. For example, a `str` can be a string OR null, which can be a pain.

Fortunately, Stof has a few features that help when working with null values.

{% hint style="info" %}
Stof only has null; it does not have an "undefined" value type (for the JS people out there). Some will argue that there is a difference between an intentional null and a value that does not exist (undefined). I agree, but this difference does not merit the complexity of another crappy value type for Stof. If you disagree, use JavaScript some more and tell me "undefined" was a good idea - I'll wait for you on our Discord server.
{% endhint %}

## Not Null Types

First on the list is the [never null type!](https://docs.stof.dev/book/types#not-null-types) Any type with a "!" postfix operator will error when assigning a null value to it, ensuring it is never null.

{% hint style="info" %}
Very helpful for field types, ensuring a field is never null. Also helpful as function return types and in function parameters, when you'd like an error thrown if a function returns null or takes a null argument.
{% endhint %}

```rust
#[main]
fn main() {
    let value: int! = 42;
    value = null; // errors
}
```

## Null Check

It's very common in languages that have a null value to check for null via if statements.

```rust
#[main]
fn main() {
    let value = null;
    if (value == null) {
        // doing null things
    }
    if (!value) {
        // also null things, but any falsey value in general
    }
}
```

Stof does have a shortcut for this, via the "??" operator. Given a left-hand side and right-hand side expression `lhs ?? rhs`, it expands into "return left-hand side if it is not null, otherwise, return the right-hand side".

{% hint style="info" %}
Notice in this example how getting a field via path is always null-checked - it will return null if the field or any object in the path does not exist, and will never throw an error.
{% endhint %}

```rust
#[main]
fn main() {
    let value = null ?? 42;
    assert_eq(value, 42);
    
    // chain them as you'd like
    const field_val = self.field.another.value ?? self.field.other ?? "default";
    assert_eq(field_val, "default");
}
```

## Optional Chain

Even with field paths always being null-checked, it's common to run into a scenario where you're not sure whether a function exists before attempting to call it (rather unique to Stof).

In many languages, the "?." chaining operator exists to check whether the value is null before attempting to use it, but this does not cover the Stof pitfall of functions not existing in the document when called.

{% hint style="info" %}
You're welcome to use "?." in Stof also! However, in most cases, this doesn't have an effect, and what you're really looking for is the "?" prefix operator.
{% endhint %}

One way to handle this is the [try-catch block](error-handling.md).

Another way is the "?" prefix operator, which null-checks an entire chain of operations at once (no matter how many function calls are in the chain).

```rust
sub: {
    object: {
        // nothing here
    }
    fn at(query: str) -> unknown { self.get(query) }
}
fn subobj() -> obj { self.sub }

#[main]
fn main() {
    // library function that does not exist
    let res = ?Std.dne(); // null

    // objects and/or functions that do not exist
    res = ?self.dne.myfunc(); // null

    // entire chain at once
    res = ?self.subobj().object.dne(); // null

    // doesn't interfere with successful calls
    res = ?self.subobj()["object"]; // self.sub.object

    // null-checks the entire chain of operations
    res = ?self.subobj()["dne"].woops().dude(); // null

    // partial ? - everything invalid before ? would error
    res = self.subobj()["object"]?.woops(); // null
}
```

{% hint style="info" %}
This operator is also helpful when combined with the "??" null-check operator, like so:

`let res = ?self.subobj()["dne"].woops().dude() ?? "default";`&#x20;

`assert_eq(res, "default");`&#x20;
{% endhint %}

## Ternary Operator

Another shortcut for a classic if-expression is the ternary operator: `condition ? expr_if_true : expr_if_false`. This can also be used to check that values exist, although typically applied elsewhere.

```rust
#[main]
fn main() {
    let value = null;
    value = value ? value : "default";
    assert_eq(value, "default");
}
```

## Block Expressions

Stof block expressions wind up in a lot of places (especially async block expressions). However, another use case is to make sure values are initialized properly.

If a value has a complex initialization sequence or a lot of dependency checks, this might be easiest. In most code editors, you can collapse away the initialization logic.

{% hint style="info" %}
Block expressions can be used to initialize field values, also!
{% endhint %}

```rust
#[main]
fn main() {
    let value = null;
    value = {
        if (value == null) "default"  // remember, no ";" means a return statement
        else value
    };
    assert_eq(value, "default");
}
```
