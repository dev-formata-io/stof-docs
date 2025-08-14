---
description: The worlds best example.
---

# Hello, World

```rust
#[main]
fn main() {
    pln("Hello, world!");
}
```

Name this file "hello.stof" and run it with the CLI: `stof run hello.stof`. The run command will execute all functions with a #\[main] attribute, each as a separate async process (no async keyword or attribute needed).

{% hint style="info" %}
This function uses the `Std.pln` library function to print to the standard output stream. The `Std` library is special in that the `Std` portion is optional. Try replacing the statement with `Std.pln("Hello, world!")`.
{% endhint %}

Stof is a document of data, though, so we can define our message as a field too:

```rust
message: "Hello, world!"

#[main]
fn main() {
    pln(self.message);
}
```

In this context, "self" is the main root (called "root") object for the document. It helps to think of JSON as an example here (and this will work just fine as-is):

```rust
{
    "message": "Hello, world!"
    
    #[main]
    fn main() {
        pln(self.message);
    }
}
```

The keywords "self" and "super" help navigate which object you'd like to work with, referencing the current object or the parent object, respectively. However, every object can be accessed absolutely by name also.

```rust
str message: "Hello, world!"; // types maintain consistency and readability

#[main]
fn main() {
    pln(root.message); // the name of this object is "root" - the default doc root
}
```
