---
description: First steps.
---

# Installation

Stof is meant to be embeddable in the environment of your choice. However, for these docs and getting started, it's recommended to use the [Stof CLI](https://crates.io/crates/stof-cli).

{% hint style="info" %}
We're working on making Stof more accessible in other languages and with additional distribution.
{% endhint %}

The easiest way to install the CLI currently is by installing Rust (cargo) and running a quick install command.

### Install Rust

Visit the [Installation page](https://doc.rust-lang.org/book/ch01-01-installation.html) for Rust and follow the instructions to install Rust on your system of choice.

### Install Stof CLI

After installing Rust, you'll also have Rust's package manager, cargo, installed. Run the following command to install the Stof CLI binary to your cargo's bin folder.

```bash
cargo install stof-cli
```

After installation, check to make sure the binary is available in your PATH. If not, make sure the cargo binary directory is added to your system PATH environment variable (bin folder is typically located under your user folder as ".cargo/bin").

```bash
stof -V
```

This will print the installed version of the CLI. You can check to see if it is the most recent by visiting crates.io or by looking at the [..](../ "mention") page.
