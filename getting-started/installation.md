---
description: Local Development
---

# Installation

Stof is meant to be embeddable in the environment of your choice. However, for these docs and getting started, it's recommended to use the [Stof CLI](https://crates.io/crates/stof-cli).

{% hint style="success" %}
The [online playground](https://play.stof.dev/) allows you to get started immediately without needing to install the CLI.
{% endhint %}

{% hint style="info" %}
Stof is also embeddable within JavaScript/TypeScript using the [JSR](https://jsr.io/@formata/stof) package. We're working on making Stof more accessible in other languages and with additional distribution.
{% endhint %}

The easiest way to install the CLI currently is by installing Rust (cargo) and running a quick install command.

## Install Rust

Visit the [Installation page](https://doc.rust-lang.org/book/ch01-01-installation.html) for Rust and follow the instructions to install Rust on your system of choice.

## Install Stof CLI

After installing Rust, you'll also have Rust's package manager, cargo, installed. Run the following command to install the Stof CLI binary to your cargo's bin folder.

```bash
cargo install stof-cli
```

After installation, check to make sure the binary is available in your PATH. If not, make sure the cargo binary directory is added to your system PATH environment variable (bin folder is typically located under your user folder as ".cargo/bin").

```bash
stof -V
```

This will print the installed version of the CLI. You can check to see if it is the most recent by visiting [crates.io](https://crates.io/crates/stof-cli).

## VS Code Syntax Highlighting

A simple VS Code extension for syntax highlighting can be found on the [marketplace](https://marketplace.visualstudio.com/items?itemName=Formata.stof). Or search for "Stof" in the VS Code extensions tab.

{% hint style="info" %}
Currently, the extension does not provide error checking, just syntax highlighting.
{% endhint %}
