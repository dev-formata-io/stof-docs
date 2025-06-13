---
icon: books
---

# Libraries

Libraries enable the inner workings of Stof as well as Stof's access to the outside world. Libraries should never travel with Stof between systems - it is up to the host environment to dictate what Stof has access to.

Host systems can add, remove, or replace libraries to extend and control Stof. By default, Stof comes with libraries for its types and a standard library for some common functionality.

{% hint style="info" %}
See [cli-libraries](../cli/cli-libraries/ "mention") for additional libraries that get added by the Stof CLI. These additional libraries (or your own libraries) can be added or replicated in your embedded environments.
{% endhint %}

To call into a library that the Stof document has loaded, simply reference it by name. For Stof types, this is done automatically when a variable is used:

```rust
fn example() {
    let x = 2.pow(3);          // reference the "Number" library based on value type
    let y = Number.pow(2, 3);  // explicitly reference the "Number" library
    
    assertEq(x, y);     // automatically reference the library named "std"
    std.assertEq(x, y); // explicitly reference the standard library "std"
}
```
