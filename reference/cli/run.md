---
description: Running a Stof document with the CLI.
---

# 🏃 Run

{% hint style="info" %}
If you haven't yet, install the [stof-cli](https://crates.io/crates/stof-cli) binary. More instructions can be found on the [resources-and-information.md](../../resources-and-information.md "mention") page.
{% endhint %}

```
> stof run <OPTIONS> <FILE>.stof
```

### Options

* <mark style="color:purple;">-a, --allow</mark> \<ALLOW>
  * Add an additional library to use by name. By default, the CLI gives access to read and write files on the system (for imports), but nothing else. If for example you want to give Stof access to the network, you'll need to pass "-a http".
    * <mark style="color:orange;">http</mark> - Gives Stof an [HTTP interface](cli-libraries/http-library.md) for making network calls.
    * <mark style="color:orange;">github</mark> - Gives Stof access to [additional files hosted on GitHub](https://github.com/dev-formata-io/stof-github) via the GitHub API. A package manger will be released soon.

### Hello World

file: <mark style="color:purple;">`hello.stof`</mark>

```rust
#[main]
fn main() {
    pln("hello world");
}
```

```
> stof run hello.stof
hello world
```

### Multiple Mains

Stof can have #\[main] functions anywhere in the document. Their order of execution is not deterministic and will change. For more info on function attributes, see [functions.md](../../common-concepts/functions.md "mention").

file: <mark style="color:purple;">`mains.stof`</mark>

```rust
#[main]
fn doSomething() {
    pln("does something");
}

#[main]
fn doAnother() {
    pln("doing another thing");
}
```

```
> stof run mains.stof
does something
doing another thing
```
