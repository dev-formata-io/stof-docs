---
description: Testing a Stof document with the CLI.
---

# 🧪 Test

{% hint style="info" %}
If you haven't yet, install the [stof-cli](https://crates.io/crates/stof-cli) binary. More instructions can be found on the [resources-and-information.md](../../resources-and-information.md "mention") page.
{% endhint %}

```
> stof test <OPTIONS> <FILE>.stof
```

### Options

* <mark style="color:purple;">-a, --allow</mark> \<ALLOW>
  * Add an additional library to use by name. By default, the CLI gives access to read and write files on the system (for imports), but nothing else. If for example you want to give Stof access to the network, you'll need to pass "-a http".
    * <mark style="color:orange;">http</mark> - Gives Stof an [HTTP interface](cli-libraries/http-library.md) for making network calls.
    * <mark style="color:orange;">github</mark> - Gives Stof access to [additional files hosted on GitHub](https://github.com/dev-formata-io/stof-github) via the GitHub API. A package manger will be released soon.

### Hello World

file: <mark style="color:purple;">`hello.stof`</mark>

```rust
#[test("hello world")]
fn hello(): str {
    return "hello world";
}
```

```
> stof test hello.stof
```

<figure><img src="../../.gitbook/assets/Screenshot 2024-12-19 183624.png" alt=""><figcaption><p>hello.stof test success output</p></figcaption></figure>

```rust
#[test("hello universe")]
fn hello(): str {
    return "hello world";
}
```

```
> stof test hello.stof
```

<figure><img src="../../.gitbook/assets/Screenshot 2024-12-19 183943.png" alt=""><figcaption><p>hello.stof test failure output</p></figcaption></figure>
